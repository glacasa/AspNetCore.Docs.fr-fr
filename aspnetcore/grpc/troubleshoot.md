---
<span data-ttu-id="9cebd-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="9cebd-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9cebd-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9cebd-102">'Blazor'</span></span>
- <span data-ttu-id="9cebd-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9cebd-103">'Identity'</span></span>
- <span data-ttu-id="9cebd-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9cebd-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="9cebd-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9cebd-105">'Razor'</span></span>
- <span data-ttu-id="9cebd-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="9cebd-106">'SignalR' uid:</span></span> 

---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="9cebd-107">Résoudre les problèmes de gRPC sur .NET Core</span><span class="sxs-lookup"><span data-stu-id="9cebd-107">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="9cebd-108">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9cebd-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="9cebd-109">Ce document traite des problèmes couramment rencontrés lors du développement d’applications gRPC sur .NET.</span><span class="sxs-lookup"><span data-stu-id="9cebd-109">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="9cebd-110">Incompatibilité entre la configuration SSL/TLS du client et du service</span><span class="sxs-lookup"><span data-stu-id="9cebd-110">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="9cebd-111">Le modèle gRPC et les exemples utilisent le protocole [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) pour sécuriser les services gRPC par défaut.</span><span class="sxs-lookup"><span data-stu-id="9cebd-111">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="9cebd-112">les clients gRPC doivent utiliser une connexion sécurisée pour appeler correctement les services gRPC sécurisés.</span><span class="sxs-lookup"><span data-stu-id="9cebd-112">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="9cebd-113">Vous pouvez vérifier que ASP.NET Core le service gRPC utilise TLS dans les journaux écrits au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="9cebd-113">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="9cebd-114">Le service est à l’écoute sur un point de terminaison HTTPs :</span><span class="sxs-lookup"><span data-stu-id="9cebd-114">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="9cebd-115">Le client .NET Core doit utiliser `https` dans l’adresse du serveur pour effectuer des appels avec une connexion sécurisée :</span><span class="sxs-lookup"><span data-stu-id="9cebd-115">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="9cebd-116">Toutes les implémentations du client gRPC prennent en charge TLS.</span><span class="sxs-lookup"><span data-stu-id="9cebd-116">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="9cebd-117">les clients gRPC d’autres langages requièrent généralement le canal configuré avec `SslCredentials` .</span><span class="sxs-lookup"><span data-stu-id="9cebd-117">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="9cebd-118">`SslCredentials`Spécifie le certificat que le client utilisera, et il doit être utilisé à la place d’informations d’identification non sécurisées.</span><span class="sxs-lookup"><span data-stu-id="9cebd-118">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="9cebd-119">Pour obtenir des exemples de configuration des différentes implémentations du client gRPC pour utiliser TLS, consultez [authentification gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="9cebd-119">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="9cebd-120">Appeler un service gRPC avec un certificat non approuvé/non valide</span><span class="sxs-lookup"><span data-stu-id="9cebd-120">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="9cebd-121">Le client .NET gRPC nécessite que le service dispose d’un certificat approuvé.</span><span class="sxs-lookup"><span data-stu-id="9cebd-121">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="9cebd-122">Le message d’erreur suivant est retourné lors de l’appel d’un service gRPC sans certificat approuvé :</span><span class="sxs-lookup"><span data-stu-id="9cebd-122">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="9cebd-123">Exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="9cebd-123">Unhandled exception.</span></span> <span data-ttu-id="9cebd-124">System .net. http. HttpRequestException : la connexion SSL n’a pas pu être établie, consultez l’exception interne.</span><span class="sxs-lookup"><span data-stu-id="9cebd-124">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="9cebd-125">---> System.Security.Authentication.AuthenticationException : Le certificat distant n’est pas valide selon la procédure de validation.</span><span class="sxs-lookup"><span data-stu-id="9cebd-125">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="9cebd-126">Vous pouvez voir cette erreur si vous testez votre application localement et que le certificat de développement ASP.NET Core HTTPs n’est pas approuvé.</span><span class="sxs-lookup"><span data-stu-id="9cebd-126">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="9cebd-127">Pour obtenir des instructions afin de résoudre ce problème, consultez [Faire confiance au certificat de développement ASP.NET Core HTTPS sur Windows et macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="9cebd-127">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="9cebd-128">Si vous appelez un service gRPC sur un autre ordinateur et que vous ne pouvez pas faire confiance au certificat, le client gRPC peut être configuré pour ignorer le certificat non valide.</span><span class="sxs-lookup"><span data-stu-id="9cebd-128">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="9cebd-129">Le code suivant utilise [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) pour autoriser les appels sans certificat approuvé :</span><span class="sxs-lookup"><span data-stu-id="9cebd-129">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="9cebd-130">Les certificats non approuvés doivent uniquement être utilisés pendant le développement d’applications.</span><span class="sxs-lookup"><span data-stu-id="9cebd-130">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="9cebd-131">Les applications de production doivent toujours utiliser des certificats valides.</span><span class="sxs-lookup"><span data-stu-id="9cebd-131">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="9cebd-132">Appeler des services gRPC non sécurisés avec un client .NET Core</span><span class="sxs-lookup"><span data-stu-id="9cebd-132">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="9cebd-133">Une configuration supplémentaire est requise pour appeler des services gRPC non sécurisés avec le client .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9cebd-133">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="9cebd-134">Le client gRPC doit définir le `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` commutateur sur `true` et l’utiliser `http` dans l’adresse du serveur :</span><span class="sxs-lookup"><span data-stu-id="9cebd-134">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="9cebd-135">Impossible de démarrer ASP.NET Core application gRPC sur macOS</span><span class="sxs-lookup"><span data-stu-id="9cebd-135">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="9cebd-136">Kestrel ne prend pas en charge HTTP/2 avec TLS sur macOS et les anciennes versions de Windows telles que Windows 7.</span><span class="sxs-lookup"><span data-stu-id="9cebd-136">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="9cebd-137">Le modèle et les exemples de ASP.NET Core gRPC utilisent TLS par défaut.</span><span class="sxs-lookup"><span data-stu-id="9cebd-137">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="9cebd-138">Le message d’erreur suivant s’affiche lorsque vous essayez de démarrer le serveur gRPC :</span><span class="sxs-lookup"><span data-stu-id="9cebd-138">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="9cebd-139">Impossible d’établir une liaison avec https://localhost:5001 sur l’interface de bouclage IPv4 : « http/2 sur TLS n’est pas pris en charge sur MacOS en raison de la prise en charge de ALPN manquante. ».</span><span class="sxs-lookup"><span data-stu-id="9cebd-139">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="9cebd-140">Pour contourner ce problème, configurez Kestrel et le client gRPC pour utiliser HTTP/2 *sans* TLS.</span><span class="sxs-lookup"><span data-stu-id="9cebd-140">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="9cebd-141">Vous ne devez effectuer cette opération qu’au cours du développement.</span><span class="sxs-lookup"><span data-stu-id="9cebd-141">You should only do this during development.</span></span> <span data-ttu-id="9cebd-142">Si vous n’utilisez pas TLS, les messages gRPC sont envoyés sans chiffrement.</span><span class="sxs-lookup"><span data-stu-id="9cebd-142">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="9cebd-143">Kestrel doit configurer un point de terminaison HTTP/2 sans TLS dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9cebd-143">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="9cebd-144">Quand un point de terminaison HTTP/2 est configuré sans TLS, le [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) du point de terminaison doit avoir la valeur `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="9cebd-144">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="9cebd-145">`HttpProtocols.Http1AndHttp2`ne peut pas être utilisé, car TLS est requis pour négocier HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9cebd-145">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="9cebd-146">Sans TLS, toutes les connexions au point de terminaison par défaut HTTP/1.1 et les appels gRPC échouent.</span><span class="sxs-lookup"><span data-stu-id="9cebd-146">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="9cebd-147">Le client gRPC doit également être configuré de façon à ne pas utiliser TLS.</span><span class="sxs-lookup"><span data-stu-id="9cebd-147">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="9cebd-148">Pour plus d’informations, consultez [appeler des services gRPC non sécurisés avec un client .net Core](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="9cebd-148">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="9cebd-149">HTTP/2 sans TLS doit être utilisé uniquement pendant le développement de l’application.</span><span class="sxs-lookup"><span data-stu-id="9cebd-149">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="9cebd-150">Les applications de production doivent toujours utiliser la sécurité de transport.</span><span class="sxs-lookup"><span data-stu-id="9cebd-150">Production apps should always use transport security.</span></span> <span data-ttu-id="9cebd-151">Pour plus d’informations, consultez Considérations sur la [sécurité dans gRPC pour ASP.net Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="9cebd-151">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="9cebd-152">les ressources C# gRPC ne sont pas générées à partir de fichiers. proto</span><span class="sxs-lookup"><span data-stu-id="9cebd-152">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="9cebd-153">la génération de code gRPC des clients concrets et des classes de base de service requiert que les fichiers et les outils protobuf soient référencés à partir d’un projet.</span><span class="sxs-lookup"><span data-stu-id="9cebd-153">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="9cebd-154">Vous devez inclure les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="9cebd-154">You must include:</span></span>

* <span data-ttu-id="9cebd-155">fichiers *. proto* que vous souhaitez utiliser dans le `<Protobuf>` groupe d’éléments.</span><span class="sxs-lookup"><span data-stu-id="9cebd-155">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="9cebd-156">Les [fichiers *. proto* importés](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) doivent être référencés par le projet.</span><span class="sxs-lookup"><span data-stu-id="9cebd-156">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="9cebd-157">Référence de package au package d’outils gRPC [gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="9cebd-157">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="9cebd-158">Pour plus d’informations sur la génération de ressources C# gRPC, consultez <xref:grpc/basics> .</span><span class="sxs-lookup"><span data-stu-id="9cebd-158">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="9cebd-159">Par défaut, une `<Protobuf>` référence génère un client concret et une classe de base de service.</span><span class="sxs-lookup"><span data-stu-id="9cebd-159">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="9cebd-160">L’attribut de l’élément de référence `GrpcServices` peut être utilisé pour limiter la génération d’éléments C#.</span><span class="sxs-lookup"><span data-stu-id="9cebd-160">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="9cebd-161">`GrpcServices`Les options valides sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="9cebd-161">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="9cebd-162">`Both`(valeur par défaut absente)</span><span class="sxs-lookup"><span data-stu-id="9cebd-162">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="9cebd-163">Une application Web ASP.NET Core hébergeant gRPC services a uniquement besoin de la classe de base de service générée :</span><span class="sxs-lookup"><span data-stu-id="9cebd-163">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="9cebd-164">Une application cliente gRPC qui effectue des appels gRPC a uniquement besoin du client concret généré :</span><span class="sxs-lookup"><span data-stu-id="9cebd-164">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="9cebd-165">Projets WPF impossible de générer des éléments gRPC C# à partir de fichiers. proto</span><span class="sxs-lookup"><span data-stu-id="9cebd-165">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="9cebd-166">Les projets WPF présentent un [problème connu](https://github.com/dotnet/wpf/issues/810) qui empêche la génération de code gRPC de fonctionner correctement.</span><span class="sxs-lookup"><span data-stu-id="9cebd-166">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="9cebd-167">Tout type gRPC généré dans un projet WPF en référençant `Grpc.Tools` les fichiers *. proto* crée des erreurs de compilation lorsqu’il est utilisé :</span><span class="sxs-lookup"><span data-stu-id="9cebd-167">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="9cebd-168">erreur CS0246 : le nom du type ou de l’espace de noms’MyGrpcServices’est introuvable (une directive using ou une référence d’assembly est-elle manquante ?)</span><span class="sxs-lookup"><span data-stu-id="9cebd-168">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="9cebd-169">Vous pouvez contourner ce problème en :</span><span class="sxs-lookup"><span data-stu-id="9cebd-169">You can workaround this issue by:</span></span>

1. <span data-ttu-id="9cebd-170">Créez un projet de bibliothèque de classes .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9cebd-170">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="9cebd-171">Dans le nouveau projet, ajoutez des références pour activer la [génération de code C# à partir des fichiers \* \* . proto\* ](xref:grpc/basics#generated-c-assets):</span><span class="sxs-lookup"><span data-stu-id="9cebd-171">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="9cebd-172">Ajoutez une référence de package au package [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="9cebd-172">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="9cebd-173">Ajoutez des fichiers \* \* . proto\* au `<Protobuf>` groupe d’éléments.</span><span class="sxs-lookup"><span data-stu-id="9cebd-173">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="9cebd-174">Dans l’application WPF, ajoutez une référence au nouveau projet.</span><span class="sxs-lookup"><span data-stu-id="9cebd-174">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="9cebd-175">L’application WPF peut utiliser les types générés par gRPC à partir du nouveau projet de bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="9cebd-175">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
