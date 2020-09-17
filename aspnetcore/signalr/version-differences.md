---
title: Différences entre SignalR et ASP.net Core SignalR
author: bradygaster
description: Différences entre SignalR et ASP.net Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 965fbb3d8007cb64aaf51d82d87ed7a3a5298e9b
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721786"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="7f828-103">Différences entre ASP.NET SignalR et ASP.net Core SignalR</span><span class="sxs-lookup"><span data-stu-id="7f828-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="7f828-104">ASP.NET Core SignalR n’est pas compatible avec les clients ou les serveurs pour ASP.NET SignalR .</span><span class="sxs-lookup"><span data-stu-id="7f828-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="7f828-105">Cet article détaille les fonctionnalités qui ont été supprimées ou modifiées dans ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="7f828-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="7f828-106">Comment identifier la SignalR version</span><span class="sxs-lookup"><span data-stu-id="7f828-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="7f828-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="7f828-107">ASP.NET SignalR</span></span> | <span data-ttu-id="7f828-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="7f828-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="7f828-109">**Package NuGet de serveur**</span><span class="sxs-lookup"><span data-stu-id="7f828-109">**Server NuGet package**</span></span> | <span data-ttu-id="7f828-110">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="7f828-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="7f828-111">Aucun.</span><span class="sxs-lookup"><span data-stu-id="7f828-111">None.</span></span> <span data-ttu-id="7f828-112">Inclus dans l’infrastructure partagée [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="7f828-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="7f828-113">**Packages NuGet client**</span><span class="sxs-lookup"><span data-stu-id="7f828-113">**Client NuGet packages**</span></span> | <span data-ttu-id="7f828-114">[Microsoft. AspNet. SignalR . Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="7f828-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="7f828-115">[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="7f828-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="7f828-116">[Microsoft. AspNetCore. SignalR . Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="7f828-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="7f828-117">**Package NPM du client JavaScript**</span><span class="sxs-lookup"><span data-stu-id="7f828-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="7f828-118">signalr</span><span class="sxs-lookup"><span data-stu-id="7f828-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="7f828-119">**Client Java**</span><span class="sxs-lookup"><span data-stu-id="7f828-119">**Java client**</span></span> | <span data-ttu-id="7f828-120">[Dépôt github](https://github.com/SignalR/java-client) (déconseillé)</span><span class="sxs-lookup"><span data-stu-id="7f828-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="7f828-121">Package Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="7f828-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="7f828-122">**Type d’application serveur**</span><span class="sxs-lookup"><span data-stu-id="7f828-122">**Server app type**</span></span> | <span data-ttu-id="7f828-123">ASP.NET (System. Web) ou auto-hôte OWIN</span><span class="sxs-lookup"><span data-stu-id="7f828-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="7f828-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7f828-124">ASP.NET Core</span></span> |
| <span data-ttu-id="7f828-125">**Plateformes serveur prises en charge**</span><span class="sxs-lookup"><span data-stu-id="7f828-125">**Supported server platforms**</span></span> | <span data-ttu-id="7f828-126">.NET Framework 4,5 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="7f828-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="7f828-127">.NET Core 3,0 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="7f828-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="7f828-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="7f828-128">ASP.NET SignalR</span></span> | <span data-ttu-id="7f828-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="7f828-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="7f828-130">**Package NuGet de serveur**</span><span class="sxs-lookup"><span data-stu-id="7f828-130">**Server NuGet package**</span></span> | <span data-ttu-id="7f828-131">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="7f828-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="7f828-132">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)</span><span class="sxs-lookup"><span data-stu-id="7f828-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="7f828-133">[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="7f828-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="7f828-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="7f828-134">(.NET Framework)</span></span> |
| <span data-ttu-id="7f828-135">**Packages NuGet client**</span><span class="sxs-lookup"><span data-stu-id="7f828-135">**Client NuGet packages**</span></span> | <span data-ttu-id="7f828-136">[Microsoft. AspNet. SignalR . Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="7f828-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="7f828-137">[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="7f828-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="7f828-138">[Microsoft. AspNetCore. SignalR . Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="7f828-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="7f828-139">**Package NPM du client JavaScript**</span><span class="sxs-lookup"><span data-stu-id="7f828-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="7f828-140">signalr</span><span class="sxs-lookup"><span data-stu-id="7f828-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="7f828-141">**Client Java**</span><span class="sxs-lookup"><span data-stu-id="7f828-141">**Java client**</span></span> | <span data-ttu-id="7f828-142">[Dépôt github](https://github.com/SignalR/java-client) (déconseillé)</span><span class="sxs-lookup"><span data-stu-id="7f828-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="7f828-143">Package Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="7f828-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="7f828-144">**Type d’application serveur**</span><span class="sxs-lookup"><span data-stu-id="7f828-144">**Server app type**</span></span> | <span data-ttu-id="7f828-145">ASP.NET (System. Web) ou auto-hôte OWIN</span><span class="sxs-lookup"><span data-stu-id="7f828-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="7f828-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7f828-146">ASP.NET Core</span></span> |
| <span data-ttu-id="7f828-147">**Plateformes serveur prises en charge**</span><span class="sxs-lookup"><span data-stu-id="7f828-147">**Supported server platforms**</span></span> | <span data-ttu-id="7f828-148">.NET Framework 4,5 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="7f828-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="7f828-149">.NET Framework 4.6.1 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="7f828-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="7f828-150">.NET Core 2,1 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="7f828-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="7f828-151">Différences de fonctionnalités</span><span class="sxs-lookup"><span data-stu-id="7f828-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="7f828-152">Reconnexions automatiques</span><span class="sxs-lookup"><span data-stu-id="7f828-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7f828-153">Dans ASP.NET SignalR :</span><span class="sxs-lookup"><span data-stu-id="7f828-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="7f828-154">Par défaut, SignalR tente de se reconnecter au serveur si la connexion est abandonnée.</span><span class="sxs-lookup"><span data-stu-id="7f828-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="7f828-155">Dans ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="7f828-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="7f828-156">Les reconnexions automatiques s’abonnent à la fois avec le [client .net](xref:signalr/dotnet-client#automatically-reconnect) et le [client JavaScript](xref:signalr/javascript-client#automatically-reconnect):</span><span class="sxs-lookup"><span data-stu-id="7f828-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7f828-157">Avant ASP.NET Core 3,0, SignalR ne prend pas en charge les reconnexions automatiques.</span><span class="sxs-lookup"><span data-stu-id="7f828-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="7f828-158">Si le client est déconnecté, l’utilisateur doit démarrer explicitement une nouvelle connexion pour se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="7f828-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="7f828-159">Dans ASP.NET SignalR , SignalR tente de se reconnecter au serveur si la connexion est abandonnée.</span><span class="sxs-lookup"><span data-stu-id="7f828-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="7f828-160">Prise en charge de protocole</span><span class="sxs-lookup"><span data-stu-id="7f828-160">Protocol support</span></span>

<span data-ttu-id="7f828-161">ASP.NET Core SignalR prend en charge JSON, ainsi qu’un nouveau protocole binaire basé sur [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="7f828-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="7f828-162">En outre, des protocoles personnalisés peuvent être créés.</span><span class="sxs-lookup"><span data-stu-id="7f828-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="7f828-163">Transports</span><span class="sxs-lookup"><span data-stu-id="7f828-163">Transports</span></span>

<span data-ttu-id="7f828-164">Le transport de trame éternel n’est pas pris en charge dans ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="7f828-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="7f828-165">Différences sur le serveur</span><span class="sxs-lookup"><span data-stu-id="7f828-165">Differences on the server</span></span>

<span data-ttu-id="7f828-166">Les ASP.NET Core les SignalR bibliothèques côté serveur sont incluses dans [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), qui est utilisé dans le modèle d' **application Web ASP.net Core** pour les Razor projets et MVC.</span><span class="sxs-lookup"><span data-stu-id="7f828-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="7f828-167">ASP.NET Core SignalR est un intergiciel ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="7f828-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="7f828-168">Elle doit être configurée en appelant <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7f828-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7f828-169">Pour configurer le routage, mappez les itinéraires aux hubs à l’intérieur de l' <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> appel de méthode dans la `Startup.Configure` méthode.</span><span class="sxs-lookup"><span data-stu-id="7f828-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="7f828-170">Pour configurer le routage, mappez les itinéraires aux hubs à l’intérieur de l' <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> appel de méthode dans la `Startup.Configure` méthode.</span><span class="sxs-lookup"><span data-stu-id="7f828-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="7f828-171">Sessions rémanentes</span><span class="sxs-lookup"><span data-stu-id="7f828-171">Sticky sessions</span></span>

<span data-ttu-id="7f828-172">Le modèle ScaleOut pour ASP.NET SignalR permet aux clients de se reconnecter et d’envoyer des messages à n’importe quel serveur de la batterie.</span><span class="sxs-lookup"><span data-stu-id="7f828-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="7f828-173">Dans ASP.NET Core SignalR , le client doit interagir avec le même serveur pendant la durée de la connexion.</span><span class="sxs-lookup"><span data-stu-id="7f828-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="7f828-174">Pour les ScaleOut utilisant des ReDim, cela signifie que les sessions rémanentes sont requises.</span><span class="sxs-lookup"><span data-stu-id="7f828-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="7f828-175">Pour ScaleOut à l’aide du [ SignalR service Azure](/azure/azure-signalr/), les sessions rémanentes ne sont pas nécessaires, car le service gère les connexions aux clients.</span><span class="sxs-lookup"><span data-stu-id="7f828-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="7f828-176">Concentrateur unique par connexion</span><span class="sxs-lookup"><span data-stu-id="7f828-176">Single hub per connection</span></span>

<span data-ttu-id="7f828-177">Dans ASP.NET Core SignalR , le modèle de connexion a été simplifié.</span><span class="sxs-lookup"><span data-stu-id="7f828-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="7f828-178">Les connexions sont établies directement sur un concentrateur unique, au lieu d’une connexion unique utilisée pour partager l’accès à plusieurs hubs.</span><span class="sxs-lookup"><span data-stu-id="7f828-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="7f828-179">Diffusion en continu</span><span class="sxs-lookup"><span data-stu-id="7f828-179">Streaming</span></span>

<span data-ttu-id="7f828-180">ASP.NET Core SignalR prend désormais en charge la [diffusion en continu des données](xref:signalr/streaming) du concentrateur vers le client.</span><span class="sxs-lookup"><span data-stu-id="7f828-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="7f828-181">État</span><span class="sxs-lookup"><span data-stu-id="7f828-181">State</span></span>

<span data-ttu-id="7f828-182">La possibilité de passer un état arbitraire entre les clients et le Hub (souvent appelé `HubState` ) a été supprimée, ainsi que la prise en charge des messages de progression.</span><span class="sxs-lookup"><span data-stu-id="7f828-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="7f828-183">Il n’existe aucun équivalent de proxy de Hub pour le moment.</span><span class="sxs-lookup"><span data-stu-id="7f828-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="7f828-184">Suppression de PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="7f828-184">PersistentConnection removal</span></span>

<span data-ttu-id="7f828-185">Dans ASP.NET Core SignalR , la classe [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) a été supprimée.</span><span class="sxs-lookup"><span data-stu-id="7f828-185">In ASP.NET Core SignalR, the [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="7f828-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="7f828-186">GlobalHost</span></span>

<span data-ttu-id="7f828-187">ASP.NET Core a intégré l’injection de dépendances dans le Framework.</span><span class="sxs-lookup"><span data-stu-id="7f828-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="7f828-188">Les services peuvent utiliser DI pour accéder à [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="7f828-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="7f828-189">L' `GlobalHost` objet utilisé dans ASP.NET SignalR pour obtenir un `HubContext` n’existe pas dans ASP.net Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="7f828-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="7f828-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="7f828-190">HubPipeline</span></span>

<span data-ttu-id="7f828-191">ASP.NET Core SignalR ne prend pas en charge les `HubPipeline` modules.</span><span class="sxs-lookup"><span data-stu-id="7f828-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="7f828-192">Différences sur le client</span><span class="sxs-lookup"><span data-stu-id="7f828-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="7f828-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="7f828-193">TypeScript</span></span>

<span data-ttu-id="7f828-194">Le SignalR client ASP.net Core est écrit dans la [machine à écrire](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="7f828-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="7f828-195">Vous pouvez écrire en JavaScript ou à la machine à écrire lors de l’utilisation du [client JavaScript](xref:signalr/javascript-client).</span><span class="sxs-lookup"><span data-stu-id="7f828-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="7f828-196">Le client JavaScript est hébergé sur NPM</span><span class="sxs-lookup"><span data-stu-id="7f828-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7f828-197">Dans les versions de ASP.NET, le client JavaScript a été obtenu via un package NuGet dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7f828-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="7f828-198">Dans les versions ASP.NET Core, le [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) package NPM contient les bibliothèques JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7f828-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="7f828-199">Ce package n’est pas inclus dans le modèle d' **application Web ASP.net Core** .</span><span class="sxs-lookup"><span data-stu-id="7f828-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="7f828-200">Utilisez NPM pour obtenir et installer le `@microsoft/signalr` package NPM.</span><span class="sxs-lookup"><span data-stu-id="7f828-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="7f828-201">Dans les versions de ASP.NET, le client JavaScript a été obtenu via un package NuGet dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7f828-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="7f828-202">Dans les versions ASP.NET Core, le [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) package NPM contient les bibliothèques JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7f828-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="7f828-203">Ce package n’est pas inclus dans le modèle d' **application Web ASP.net Core** .</span><span class="sxs-lookup"><span data-stu-id="7f828-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="7f828-204">Utilisez NPM pour obtenir et installer le `@aspnet/signalr` package NPM.</span><span class="sxs-lookup"><span data-stu-id="7f828-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="7f828-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="7f828-205">jQuery</span></span>

<span data-ttu-id="7f828-206">La dépendance sur jQuery a été supprimée, mais les projets peuvent toujours utiliser jQuery.</span><span class="sxs-lookup"><span data-stu-id="7f828-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="7f828-207">Prise en charge d’Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="7f828-207">Internet Explorer support</span></span>

<span data-ttu-id="7f828-208">ASP.NET Core SignalR nécessite Microsoft Internet Explorer 11 ou version ultérieure (ASP.NET SignalR pris en charge par Microsoft Internet Explorer 8 et versions ultérieures).</span><span class="sxs-lookup"><span data-stu-id="7f828-208">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="7f828-209">Syntaxe de méthode du client JavaScript</span><span class="sxs-lookup"><span data-stu-id="7f828-209">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7f828-210">La syntaxe JavaScript a été modifiée par rapport à la version ASP.NET de SignalR .</span><span class="sxs-lookup"><span data-stu-id="7f828-210">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="7f828-211">Au lieu d’utiliser l' `$connection` objet, créez une connexion à l’aide de l’API [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="7f828-211">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="7f828-212">Utilisez la méthode [on](/javascript/api/@microsoft/signalr/HubConnection#on) pour spécifier les méthodes clientes que le hub peut appeler.</span><span class="sxs-lookup"><span data-stu-id="7f828-212">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="7f828-213">La syntaxe JavaScript a été modifiée par rapport à la version ASP.NET de SignalR .</span><span class="sxs-lookup"><span data-stu-id="7f828-213">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="7f828-214">Au lieu d’utiliser l' `$connection` objet, créez une connexion à l’aide de l’API [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="7f828-214">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="7f828-215">Utilisez la méthode [on](/javascript/api/@aspnet/signalr/HubConnection#on) pour spécifier les méthodes clientes que le hub peut appeler.</span><span class="sxs-lookup"><span data-stu-id="7f828-215">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="7f828-216">Après la création de la méthode cliente, démarrez la connexion Hub.</span><span class="sxs-lookup"><span data-stu-id="7f828-216">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="7f828-217">Chaînez une méthode [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) pour consigner ou gérer les erreurs.</span><span class="sxs-lookup"><span data-stu-id="7f828-217">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="7f828-218">Proxies Hub</span><span class="sxs-lookup"><span data-stu-id="7f828-218">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7f828-219">Les proxys de concentrateur ne sont plus générés automatiquement.</span><span class="sxs-lookup"><span data-stu-id="7f828-219">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="7f828-220">Au lieu de cela, le nom de la méthode est passé à l’API [Invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) en tant que chaîne.</span><span class="sxs-lookup"><span data-stu-id="7f828-220">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="7f828-221">Les proxys de concentrateur ne sont plus générés automatiquement.</span><span class="sxs-lookup"><span data-stu-id="7f828-221">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="7f828-222">Au lieu de cela, le nom de la méthode est passé à l’API [Invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) en tant que chaîne.</span><span class="sxs-lookup"><span data-stu-id="7f828-222">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="7f828-223">.NET et autres clients</span><span class="sxs-lookup"><span data-stu-id="7f828-223">.NET and other clients</span></span>

<span data-ttu-id="7f828-224">[Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Le package NuGet client contient les bibliothèques clientes .net pour ASP.net Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="7f828-224">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="7f828-225">Utilisez le <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> pour créer et générer une instance d’une connexion à un concentrateur.</span><span class="sxs-lookup"><span data-stu-id="7f828-225">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="7f828-226">Différences ScaleOut</span><span class="sxs-lookup"><span data-stu-id="7f828-226">Scaleout differences</span></span>

<span data-ttu-id="7f828-227">ASP.NET SignalR prend en charge les SQL Server et les ReDim.</span><span class="sxs-lookup"><span data-stu-id="7f828-227">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="7f828-228">ASP.NET Core SignalR prend en charge le SignalR service Azure et les ReDim.</span><span class="sxs-lookup"><span data-stu-id="7f828-228">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="7f828-229">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="7f828-229">ASP.NET</span></span>

* [<span data-ttu-id="7f828-230">SignalR ScaleOut avec Azure Service Bus</span><span class="sxs-lookup"><span data-stu-id="7f828-230">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="7f828-231">SignalR ScaleOut avec Redims</span><span class="sxs-lookup"><span data-stu-id="7f828-231">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="7f828-232">SignalR ScaleOut avec SQL Server</span><span class="sxs-lookup"><span data-stu-id="7f828-232">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="7f828-233">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7f828-233">ASP.NET Core</span></span>

* [<span data-ttu-id="7f828-234">SignalRService Azure</span><span class="sxs-lookup"><span data-stu-id="7f828-234">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="7f828-235">Fond de panier ReDim</span><span class="sxs-lookup"><span data-stu-id="7f828-235">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="7f828-236">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7f828-236">Additional resources</span></span>

* [<span data-ttu-id="7f828-237">Hubs</span><span class="sxs-lookup"><span data-stu-id="7f828-237">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="7f828-238">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="7f828-238">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="7f828-239">Client .NET</span><span class="sxs-lookup"><span data-stu-id="7f828-239">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7f828-240">Plateformes prises en charge</span><span class="sxs-lookup"><span data-stu-id="7f828-240">Supported platforms</span></span>](xref:signalr/supported-platforms)