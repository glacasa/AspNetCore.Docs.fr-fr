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
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="73eea-103">Quoi de neuf dans ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="73eea-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="73eea-104">Cet article met en évidence les changements les plus significatifs dans ASP.NET Core 3.0 avec des liens vers la documentation pertinente.</span><span class="sxs-lookup"><span data-stu-id="73eea-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

Blazor<span data-ttu-id="73eea-105">est un nouveau cadre dans ASP.NET Core pour la construction d’interface utilisateur web interactive côté client avec .NET:</span><span class="sxs-lookup"><span data-stu-id="73eea-105"> is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="73eea-106">Créez des interfaces utilisateur interactives riches à l’aide de C# plutôt que JavaScript.</span><span class="sxs-lookup"><span data-stu-id="73eea-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="73eea-107">Partagez la logique d’application côté serveur et côté client écrite dans .NET.</span><span class="sxs-lookup"><span data-stu-id="73eea-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="73eea-108">Affichez l’interface utilisateur en langage HTML et CSS pour une large prise en charge des navigateurs, y compris les navigateurs mobiles.</span><span class="sxs-lookup"><span data-stu-id="73eea-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

Blazor<span data-ttu-id="73eea-109">scénarios soutenus par le cadre :</span><span class="sxs-lookup"><span data-stu-id="73eea-109"> framework supported scenarios:</span></span>

* <span data-ttu-id="73eea-110">Composants d’interface utilisateur réutilisables (composants Razor)</span><span class="sxs-lookup"><span data-stu-id="73eea-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="73eea-111">Itinéraire côté client</span><span class="sxs-lookup"><span data-stu-id="73eea-111">Client-side routing</span></span>
* <span data-ttu-id="73eea-112">Dispositions des composants</span><span class="sxs-lookup"><span data-stu-id="73eea-112">Component layouts</span></span>
* <span data-ttu-id="73eea-113">Soutien à l’injection de dépendance</span><span class="sxs-lookup"><span data-stu-id="73eea-113">Support for dependency injection</span></span>
* <span data-ttu-id="73eea-114">Formulaires et validation</span><span class="sxs-lookup"><span data-stu-id="73eea-114">Forms and validation</span></span>
* <span data-ttu-id="73eea-115">Construire des bibliothèques de composants avec des bibliothèques de classe Razor</span><span class="sxs-lookup"><span data-stu-id="73eea-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="73eea-116">Interopérabilité JavaScript</span><span class="sxs-lookup"><span data-stu-id="73eea-116">JavaScript interop</span></span>

<span data-ttu-id="73eea-117">Pour plus d’informations, consultez <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="73eea-117">For more information, see <xref:blazor/index>.</span></span>

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="73eea-118">Serveur</span><span class="sxs-lookup"><span data-stu-id="73eea-118"> Server</span></span>

Blazor<span data-ttu-id="73eea-119">découple la logique de rendu des composants de la façon dont les mises à jour de l’interface utilisateur sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="73eea-119"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="73eea-120">Server fournit un support pour l’hébergement des composants Razor sur le serveur dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73eea-120"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="73eea-121">Les mises à jour SignalR de l’interface utilisateur sont traitées sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="73eea-121">UI updates are handled over a SignalR connection.</span></span> Blazor<span data-ttu-id="73eea-122">Le serveur est pris en charge dans ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="73eea-122"> Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="opno-locblazor-webassembly-preview"></a>Blazor<span data-ttu-id="73eea-123">WebAssembly (Avant-première)</span><span class="sxs-lookup"><span data-stu-id="73eea-123"> WebAssembly (Preview)</span></span>

Blazor<span data-ttu-id="73eea-124">les applications peuvent également être exécutées directement dans le navigateur à l’aide d’un temps d’exécution .NET basé sur WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="73eea-124"> apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> Blazor<span data-ttu-id="73eea-125">WebAssembly est en avant-première et *n’est pas* pris en charge dans ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="73eea-125"> WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> Blazor<span data-ttu-id="73eea-126">WebAssembly sera pris en charge dans une prochaine version de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73eea-126"> WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="73eea-127">Composants de rasoir</span><span class="sxs-lookup"><span data-stu-id="73eea-127">Razor components</span></span>

Blazor<span data-ttu-id="73eea-128">les applications sont construites à partir de composants.</span><span class="sxs-lookup"><span data-stu-id="73eea-128"> apps are built from components.</span></span> <span data-ttu-id="73eea-129">Les composants sont des morceaux autonomes de l’interface utilisateur (UI), tels qu’une page, un dialogue ou une forme.</span><span class="sxs-lookup"><span data-stu-id="73eea-129">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="73eea-130">Les composants sont des classes normales .NET qui définissent la logique de rendu de l’interface utilisateur et les gestionnaires d’événements côté client.</span><span class="sxs-lookup"><span data-stu-id="73eea-130">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="73eea-131">Vous pouvez créer de riches applications web interactives sans JavaScript.</span><span class="sxs-lookup"><span data-stu-id="73eea-131">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="73eea-132">Les composants Blazor sont généralement rédigés à l’aide de la syntaxe Razor, un mélange naturel de HTML et de C.</span><span class="sxs-lookup"><span data-stu-id="73eea-132">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="73eea-133">Les composants Razor sont similaires aux vues Razor Pages et MVC en ce sens qu’ils utilisent tous les deux Razor.</span><span class="sxs-lookup"><span data-stu-id="73eea-133">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="73eea-134">Contrairement aux pages et aux vues, qui sont basées sur un modèle de réponse aux demandes, les composants sont utilisés spécifiquement pour le traitement de la composition de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="73eea-134">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="73eea-135">gRPC</span><span class="sxs-lookup"><span data-stu-id="73eea-135">gRPC</span></span>

<span data-ttu-id="73eea-136">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="73eea-136">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="73eea-137">Est un cadre populaire, haute performance RPC (appel procédure à distance).</span><span class="sxs-lookup"><span data-stu-id="73eea-137">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="73eea-138">Offre une approche d’opinion du développement de l’API.</span><span class="sxs-lookup"><span data-stu-id="73eea-138">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="73eea-139">Utilise des technologies modernes telles que :</span><span class="sxs-lookup"><span data-stu-id="73eea-139">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="73eea-140">HTTP/2 pour le transport.</span><span class="sxs-lookup"><span data-stu-id="73eea-140">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="73eea-141">Protocole Buffers comme langage de description d’interface.</span><span class="sxs-lookup"><span data-stu-id="73eea-141">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="73eea-142">Format de sérialisation binaire.</span><span class="sxs-lookup"><span data-stu-id="73eea-142">Binary serialization format.</span></span>
* <span data-ttu-id="73eea-143">Fournit des fonctionnalités telles que :</span><span class="sxs-lookup"><span data-stu-id="73eea-143">Provides features such as:</span></span>

  * <span data-ttu-id="73eea-144">Authentification</span><span class="sxs-lookup"><span data-stu-id="73eea-144">Authentication</span></span>
  * <span data-ttu-id="73eea-145">Streaming bidirectionnel et contrôle des flux.</span><span class="sxs-lookup"><span data-stu-id="73eea-145">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="73eea-146">Annulation et délais d’attente.</span><span class="sxs-lookup"><span data-stu-id="73eea-146">Cancellation and timeouts.</span></span>

<span data-ttu-id="73eea-147">GRPC fonctionnalité dans ASP.NET Core 3.0 comprend:</span><span class="sxs-lookup"><span data-stu-id="73eea-147">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="73eea-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span><span class="sxs-lookup"><span data-stu-id="73eea-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="73eea-149">gRPC sur ASP.NET Core s’intègre aux caractéristiques standard ASP.NET Core comme l’exploitation forestière, l’injection de dépendance (DI), l’authentification et l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="73eea-149">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="73eea-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; Un client gRPC pour .NET Core `HttpClient`qui s’appuie sur le familier .</span><span class="sxs-lookup"><span data-stu-id="73eea-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="73eea-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC intégration `HttpClientFactory`client avec .</span><span class="sxs-lookup"><span data-stu-id="73eea-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="73eea-152">Pour plus d’informations, consultez <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="73eea-152">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="73eea-153">Voir [ SignalR le code de mise à jour](xref:migration/22-to-30#signalr) pour les instructions de migration.</span><span class="sxs-lookup"><span data-stu-id="73eea-153">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> SignalR<span data-ttu-id="73eea-154">utilise `System.Text.Json` maintenant pour sérialiser/déséialiser les messages JSON.</span><span class="sxs-lookup"><span data-stu-id="73eea-154"> now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="73eea-155">Consultez [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) pour `Newtonsoft.Json`obtenir des instructions pour restaurer le sérialisateur basé sur le site.</span><span class="sxs-lookup"><span data-stu-id="73eea-155">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="73eea-156">Dans le JavaScript et SignalR.NET Clients pour , le support a été ajouté pour la reconnexion automatique.</span><span class="sxs-lookup"><span data-stu-id="73eea-156">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="73eea-157">Par défaut, le client tente de se reconnecter immédiatement et de réessayer après 2, 10 et 30 secondes si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="73eea-157">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="73eea-158">Si le client se reconnecte avec succès, il reçoit une nouvelle pièce d’identité de connexion.</span><span class="sxs-lookup"><span data-stu-id="73eea-158">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="73eea-159">La reconnexion automatique est l’adhésion :</span><span class="sxs-lookup"><span data-stu-id="73eea-159">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="73eea-160">Les intervalles de reconnexion peuvent être spécifiés en passant une gamme de durées basées sur la milliseconde :</span><span class="sxs-lookup"><span data-stu-id="73eea-160">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="73eea-161">Une implémentation personnalisée peut être transmise pour le plein contrôle des intervalles de reconnexion.</span><span class="sxs-lookup"><span data-stu-id="73eea-161">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="73eea-162">Si la reconnexion échoue après le dernier intervalle de reconnexion :</span><span class="sxs-lookup"><span data-stu-id="73eea-162">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="73eea-163">Le client considère que la connexion est hors connexion.</span><span class="sxs-lookup"><span data-stu-id="73eea-163">The client considers the connection is offline.</span></span>
* <span data-ttu-id="73eea-164">Le client cesse d’essayer de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="73eea-164">The client stops trying to reconnect.</span></span>

<span data-ttu-id="73eea-165">Lors des tentatives de reconnexion, mettez à jour l’interface utilisateur de l’application pour informer l’utilisateur que la reconnexion est tentée.</span><span class="sxs-lookup"><span data-stu-id="73eea-165">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="73eea-166">Pour fournir des commentaires sur l’interface SignalR utilisateur lorsque la connexion est interrompue, l’API client a été élargie pour inclure les gestionnaires d’événements suivants :</span><span class="sxs-lookup"><span data-stu-id="73eea-166">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="73eea-167">`onreconnecting`: Donne aux développeurs la possibilité de désactiver l’interface utilisateur ou de faire savoir aux utilisateurs que l’application est hors ligne.</span><span class="sxs-lookup"><span data-stu-id="73eea-167">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="73eea-168">`onreconnected`: Donne aux développeurs l’occasion de mettre à jour l’interface utilisateur une fois la connexion rétablie.</span><span class="sxs-lookup"><span data-stu-id="73eea-168">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="73eea-169">Le code `onreconnecting` suivant utilise pour mettre à jour l’interface utilisateur tout en essayant de se connecter:</span><span class="sxs-lookup"><span data-stu-id="73eea-169">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="73eea-170">Le code `onreconnected` suivant utilise pour mettre à jour l’interface utilisateur sur la connexion :</span><span class="sxs-lookup"><span data-stu-id="73eea-170">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR<span data-ttu-id="73eea-171">3.0 et plus tard fournit une ressource personnalisée aux gestionnaires d’autorisation lorsqu’une méthode de plaque tournante nécessite une autorisation.</span><span class="sxs-lookup"><span data-stu-id="73eea-171"> 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="73eea-172">La ressource est `HubInvocationContext`un exemple de .</span><span class="sxs-lookup"><span data-stu-id="73eea-172">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="73eea-173">Le `HubInvocationContext` comprend le:</span><span class="sxs-lookup"><span data-stu-id="73eea-173">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="73eea-174">Nom de la méthode du hub invoquée.</span><span class="sxs-lookup"><span data-stu-id="73eea-174">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="73eea-175">Arguments à la méthode de moyeu.</span><span class="sxs-lookup"><span data-stu-id="73eea-175">Arguments to the hub method.</span></span>

<span data-ttu-id="73eea-176">Prenons l’exemple suivant d’une application de salle de chat permettant à plusieurs organisations de vous connecter via Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="73eea-176">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="73eea-177">Toute personne ayant un compte Microsoft peut se connecter pour discuter, mais seuls les membres de l’organisation propriétaire peuvent interdire les utilisateurs ou afficher les historiques de chat des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="73eea-177">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="73eea-178">L’application pourrait restreindre certaines fonctionnalités de certains utilisateurs spécifiques.</span><span class="sxs-lookup"><span data-stu-id="73eea-178">The app could restrict certain functionality from specific users.</span></span>

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

<span data-ttu-id="73eea-179">Dans le code `DomainRestrictedRequirement` précédent, `IAuthorizationRequirement`sert de coutume .</span><span class="sxs-lookup"><span data-stu-id="73eea-179">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="73eea-180">Étant `HubInvocationContext` donné que le paramètre de ressources est adopté, la logique interne peut :</span><span class="sxs-lookup"><span data-stu-id="73eea-180">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="73eea-181">Inspectez le contexte dans lequel le Hub est appelé.</span><span class="sxs-lookup"><span data-stu-id="73eea-181">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="73eea-182">Prenez des décisions en permettant à l’utilisateur d’exécuter des méthodes Hub individuelles.</span><span class="sxs-lookup"><span data-stu-id="73eea-182">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="73eea-183">Les méthodes Hub individuelles peuvent être marquées avec le nom de la stratégie que le code vérifie au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="73eea-183">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="73eea-184">Alors que les clients tentent `DomainRestrictedRequirement` d’appeler des méthodes Hub individuelles, le gestionnaire exécute et contrôle l’accès aux méthodes.</span><span class="sxs-lookup"><span data-stu-id="73eea-184">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="73eea-185">D’après la `DomainRestrictedRequirement` façon dont les contrôles accèdent :</span><span class="sxs-lookup"><span data-stu-id="73eea-185">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="73eea-186">Tous les utilisateurs connectés peuvent `SendMessage` appeler la méthode.</span><span class="sxs-lookup"><span data-stu-id="73eea-186">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="73eea-187">Seuls les utilisateurs qui se `@jabbr.net` sont connectés avec une adresse e-mail peuvent afficher l’historique des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="73eea-187">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="73eea-188">Seuls `bob42@jabbr.net` les utilisateurs peuvent interdire les utilisateurs de la salle de chat.</span><span class="sxs-lookup"><span data-stu-id="73eea-188">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

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

<span data-ttu-id="73eea-189">La `DomainRestricted` création de la politique pourrait impliquer :</span><span class="sxs-lookup"><span data-stu-id="73eea-189">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="73eea-190">En *Startup.cs*, en ajoutant la nouvelle politique.</span><span class="sxs-lookup"><span data-stu-id="73eea-190">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="73eea-191">Fournir l’exigence personnalisée `DomainRestrictedRequirement` comme paramètre.</span><span class="sxs-lookup"><span data-stu-id="73eea-191">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="73eea-192">Enregistrement `DomainRestricted` auprès du middleware d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="73eea-192">Registering `DomainRestricted` with the authorization middleware.</span></span>

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

SignalR<span data-ttu-id="73eea-193">hubs utilisent [Endpoint Routing](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="73eea-193"> hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> SignalR<span data-ttu-id="73eea-194">la connexion hub a déjà été effectuée explicitement :</span><span class="sxs-lookup"><span data-stu-id="73eea-194"> hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="73eea-195">Dans la version précédente, les développeurs avaient besoin de transférer les contrôleurs, les pages Razor et les moyeux dans une variété d’endroits.</span><span class="sxs-lookup"><span data-stu-id="73eea-195">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="73eea-196">La connexion explicite se traduit par une série de segments de routage presque identiques :</span><span class="sxs-lookup"><span data-stu-id="73eea-196">Explicit connection results in a series of nearly-identical routing segments:</span></span>

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

SignalR<span data-ttu-id="73eea-197">3.0 hubs peuvent être acheminés via le routage de point final.</span><span class="sxs-lookup"><span data-stu-id="73eea-197"> 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="73eea-198">Avec le routage de point de terminaison, généralement tous les itinéraires peuvent être configurés dans `UseRouting`:</span><span class="sxs-lookup"><span data-stu-id="73eea-198">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="73eea-199">ASP.NET Core 3.0 SignalR a ajouté :</span><span class="sxs-lookup"><span data-stu-id="73eea-199">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="73eea-200">Streaming client-serveur.</span><span class="sxs-lookup"><span data-stu-id="73eea-200">Client-to-server streaming.</span></span> <span data-ttu-id="73eea-201">Avec le streaming client-serveur, les méthodes côté serveur `IAsyncEnumerable<T>` `ChannelReader<T>`peuvent prendre des exemples d’un ou .</span><span class="sxs-lookup"><span data-stu-id="73eea-201">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="73eea-202">Dans l’échantillon C `UploadStream` SUIVANT, la méthode du Hub recevra un flux de chaînes du client :</span><span class="sxs-lookup"><span data-stu-id="73eea-202">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="73eea-203">.NET applications client peuvent `IAsyncEnumerable<T>` passer `ChannelReader<T>` soit `stream` un ou `UploadStream` une instance comme l’argument de la méthode Hub ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="73eea-203">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="73eea-204">Une `for` fois la boucle terminée et les sorties de la fonction locale, l’achèvement du flux est envoyé :</span><span class="sxs-lookup"><span data-stu-id="73eea-204">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

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

<span data-ttu-id="73eea-205">Les applications client SignalR `Subject` JavaScript utilisent le (ou un `stream` sujet `UploadStream` [RxJS](https://rxjs.dev/api/index/class/Subject)) pour l’argument de la méthode Hub ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="73eea-205">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="73eea-206">Le code JavaScript `subject.next` pourrait utiliser la méthode pour gérer les chaînes au fur et à mesure qu’elles sont capturées et prêtes à être envoyées au serveur.</span><span class="sxs-lookup"><span data-stu-id="73eea-206">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="73eea-207">À l’aide de code comme les deux extraits précédents, des expériences de streaming en temps réel peuvent être créées.</span><span class="sxs-lookup"><span data-stu-id="73eea-207">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="73eea-208">Nouvelle sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="73eea-208">New JSON serialization</span></span>

<span data-ttu-id="73eea-209">ASP.NET Core 3.0 utilise <xref:System.Text.Json> désormais par défaut pour la sérialisation JSON :</span><span class="sxs-lookup"><span data-stu-id="73eea-209">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="73eea-210">Lit et écrit JSON asynchronement.</span><span class="sxs-lookup"><span data-stu-id="73eea-210">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="73eea-211">Est optimisé pour le texte UTF-8.</span><span class="sxs-lookup"><span data-stu-id="73eea-211">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="73eea-212">Des performances `Newtonsoft.Json`généralement supérieures à .</span><span class="sxs-lookup"><span data-stu-id="73eea-212">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="73eea-213">Pour ajouter Json.NET à ASP.NET Core 3.0, voir [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="73eea-213">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="73eea-214">Nouvelles directives Razor</span><span class="sxs-lookup"><span data-stu-id="73eea-214">New Razor directives</span></span>

<span data-ttu-id="73eea-215">La liste suivante contient de nouvelles directives Razor :</span><span class="sxs-lookup"><span data-stu-id="73eea-215">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="73eea-216">[`@attribute`](xref:mvc/views/razor#attribute)&ndash; La `@attribute` directive applique l’attribut donné à la classe de la page ou de la vue générée.</span><span class="sxs-lookup"><span data-stu-id="73eea-216">[`@attribute`](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="73eea-217">Par exemple : `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="73eea-217">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="73eea-218">[`@implements`](xref:mvc/views/razor#implements)&ndash; La `@implements` directive met en œuvre une interface pour la classe générée.</span><span class="sxs-lookup"><span data-stu-id="73eea-218">[`@implements`](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="73eea-219">Par exemple : `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="73eea-219">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="73eea-220">IdentityServer4 prend en charge l’authentification et l’autorisation des API web et des APE</span><span class="sxs-lookup"><span data-stu-id="73eea-220">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="73eea-221">ASP.NET Core 3.0 offre l’authentification dans les applications à page unique (ZSP) en utilisant le support pour l’autorisation d’API Web.</span><span class="sxs-lookup"><span data-stu-id="73eea-221">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="73eea-222">ASP.NET’identité de base pour l’authentification et le stockage des utilisateurs est combinée avec [IdentityServer4](https://identityserver.io/) pour la mise en œuvre de Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="73eea-222">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="73eea-223">IdentityServer4 est un cadre OpenID Connect et OAuth 2.0 pour ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="73eea-223">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="73eea-224">Il permet les caractéristiques de sécurité suivantes :</span><span class="sxs-lookup"><span data-stu-id="73eea-224">It enables the following security features:</span></span>

* <span data-ttu-id="73eea-225">Authentification en tant que service (AaaS)</span><span class="sxs-lookup"><span data-stu-id="73eea-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="73eea-226">Signature unique/off (SSO) sur plusieurs types d’applications</span><span class="sxs-lookup"><span data-stu-id="73eea-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="73eea-227">Contrôle d’accès pour les API</span><span class="sxs-lookup"><span data-stu-id="73eea-227">Access control for APIs</span></span>
* <span data-ttu-id="73eea-228">Porte d’entrée de la Fédération</span><span class="sxs-lookup"><span data-stu-id="73eea-228">Federation Gateway</span></span>

<span data-ttu-id="73eea-229">Pour plus d’informations, consultez [la documentation IdentityServer4](http://docs.identityserver.io/en/latest/index.html) ou [l’authentification et l’autorisation pour les APE](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="73eea-229">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="73eea-230">Certificate et authentification Kerberos</span><span class="sxs-lookup"><span data-stu-id="73eea-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="73eea-231">L’authentification des certificats exige :</span><span class="sxs-lookup"><span data-stu-id="73eea-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="73eea-232">Configurer le serveur pour accepter les certificats.</span><span class="sxs-lookup"><span data-stu-id="73eea-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="73eea-233">Ajout de l’authentification middleware dans `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="73eea-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="73eea-234">Ajout du service d’authentification du certificat dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="73eea-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="73eea-235">Les options d’authentification des certificats comprennent la possibilité de :</span><span class="sxs-lookup"><span data-stu-id="73eea-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="73eea-236">Acceptez les certificats autosuffants.</span><span class="sxs-lookup"><span data-stu-id="73eea-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="73eea-237">Vérifiez la révocation du certificat.</span><span class="sxs-lookup"><span data-stu-id="73eea-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="73eea-238">Vérifiez que le certificat délivré a les bons drapeaux d’utilisation en elle.</span><span class="sxs-lookup"><span data-stu-id="73eea-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="73eea-239">Un mandant utilisateur par défaut est construit à partir des propriétés du certificat.</span><span class="sxs-lookup"><span data-stu-id="73eea-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="73eea-240">Le principal utilisateur contient un événement qui permet de compléter ou de remplacer le mandant.</span><span class="sxs-lookup"><span data-stu-id="73eea-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="73eea-241">Pour plus d’informations, consultez <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="73eea-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="73eea-242">[L’authentification de Windows](/windows-server/security/windows-authentication/windows-authentication-overview) a été étendue à Linux et macOS.</span><span class="sxs-lookup"><span data-stu-id="73eea-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="73eea-243">Dans les versions précédentes, Windows Authentication a été limitée à [l’IIS](xref:host-and-deploy/iis/index) et [HttpSys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="73eea-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="73eea-244">Dans ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) a la possibilité d’utiliser Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), et [NTLM sur Windows](/windows-server/security/kerberos/ntlm-overview), Linux, et macOS pour windows de domaine rejoint hôtes.</span><span class="sxs-lookup"><span data-stu-id="73eea-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="73eea-245">Le support Kestrel de ces systèmes d’authentification est fourni par le paquet [NuGet Microsoft.AspNetCore.Authentication.Negotiate.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)</span><span class="sxs-lookup"><span data-stu-id="73eea-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="73eea-246">Comme avec les autres services d’authentification, configurer l’application d’authentification large, puis configurer le service:</span><span class="sxs-lookup"><span data-stu-id="73eea-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

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

<span data-ttu-id="73eea-247">Exigences de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="73eea-247">Host requirements:</span></span>

* <span data-ttu-id="73eea-248">Les hôtes Windows doivent avoir [des noms principaux de service](/windows/win32/ad/service-principal-names) (SN) ajoutés au compte utilisateur hébergeant l’application.</span><span class="sxs-lookup"><span data-stu-id="73eea-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="73eea-249">Les machines Linux et macOS doivent être jointes au domaine.</span><span class="sxs-lookup"><span data-stu-id="73eea-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="73eea-250">Les SPN doivent être créés pour le processus web.</span><span class="sxs-lookup"><span data-stu-id="73eea-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="73eea-251">[Les fichiers Keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) doivent être générés et configurés sur la machine hôte.</span><span class="sxs-lookup"><span data-stu-id="73eea-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="73eea-252">Pour plus d’informations, consultez <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="73eea-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="73eea-253">Modifications de modèle</span><span class="sxs-lookup"><span data-stu-id="73eea-253">Template changes</span></span>

<span data-ttu-id="73eea-254">Les modèles d’interface utilisateur Web (Razor Pages, MVC avec contrôleur et vues) ont supprimé ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="73eea-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="73eea-255">L’interface utilisateur de consentement à un cookie n’est plus incluse.</span><span class="sxs-lookup"><span data-stu-id="73eea-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="73eea-256">Pour activer la fonction de consentement au cookie dans une application <xref:security/gdpr>générée par ASP.NET Core 3.0, voir .</span><span class="sxs-lookup"><span data-stu-id="73eea-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="73eea-257">Les scripts et les actifs statiques connexes sont maintenant référencés comme des fichiers locaux au lieu d’utiliser des CDN.</span><span class="sxs-lookup"><span data-stu-id="73eea-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="73eea-258">Pour plus d’informations, voir [Scripts et actifs statiques connexes sont maintenant référencés comme des fichiers locaux au lieu d’utiliser des CDN en fonction de l’environnement actuel (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="73eea-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="73eea-259">Le modèle angulaire mis à jour pour utiliser Angular 8.</span><span class="sxs-lookup"><span data-stu-id="73eea-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="73eea-260">Le modèle de bibliothèque de classe Razor (RCL) par défaut au développement de composants Razor par défaut.</span><span class="sxs-lookup"><span data-stu-id="73eea-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="73eea-261">Une nouvelle option de modèle dans Visual Studio fournit un support de modèle pour les pages et les vues.</span><span class="sxs-lookup"><span data-stu-id="73eea-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="73eea-262">Lors de la création d’un RCL à `--support-pages-and-views` partir`dotnet new razorclasslib --support-pages-and-views`du modèle dans une coque de commande, passez l’option ( ).</span><span class="sxs-lookup"><span data-stu-id="73eea-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="73eea-263">Hôte générique</span><span class="sxs-lookup"><span data-stu-id="73eea-263">Generic Host</span></span>

<span data-ttu-id="73eea-264">Les modèles ASP.NET Core 3.0 <xref:fundamentals/host/generic-host>utilisent .</span><span class="sxs-lookup"><span data-stu-id="73eea-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="73eea-265">Versions précédentes utilisées <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="73eea-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="73eea-266">L’utilisation de l’hôte générique de base .NET (<xref:Microsoft.Extensions.Hosting.HostBuilder>) fournit une meilleure intégration des applications ASP.NET Core avec d’autres scénarios de serveur qui ne sont pas spécifiques au Web.</span><span class="sxs-lookup"><span data-stu-id="73eea-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="73eea-267">Pour plus d’informations, voir [HostBuilder remplace WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="73eea-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="73eea-268">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="73eea-268">Host configuration</span></span>

<span data-ttu-id="73eea-269">Avant la sortie de ASP.NET Core 3.0, les variables `ASPNETCORE_` de l’environnement préfixées ont été chargées pour la configuration hôte de l’hébergeur.</span><span class="sxs-lookup"><span data-stu-id="73eea-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="73eea-270">Dans 3.0, `AddEnvironmentVariables` est utilisé pour charger `DOTNET_` les variables `CreateDefaultBuilder`de l’environnement préfixées avec pour la configuration de l’hôte avec .</span><span class="sxs-lookup"><span data-stu-id="73eea-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="73eea-271">Modifications apportées à l’injection de constructeurs startup</span><span class="sxs-lookup"><span data-stu-id="73eea-271">Changes to Startup constructor injection</span></span>

<span data-ttu-id="73eea-272">L’hôte générique ne prend `Startup` en charge que les types suivants pour l’injection de constructeurs :</span><span class="sxs-lookup"><span data-stu-id="73eea-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="73eea-273">Tous les services peuvent encore être `Startup.Configure` injectés directement comme arguments à la méthode.</span><span class="sxs-lookup"><span data-stu-id="73eea-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="73eea-274">Pour plus d’informations, voir [Generic Host limite l’injection de constructeurs startup (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="73eea-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="73eea-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="73eea-275">Kestrel</span></span>

* <span data-ttu-id="73eea-276">La configuration Kestrel a été mise à jour pour la migration vers l’hôte générique.</span><span class="sxs-lookup"><span data-stu-id="73eea-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="73eea-277">Dans 3.0, Kestrel est configuré sur `ConfigureWebHostDefaults`le constructeur d’hébergeur web fourni par .</span><span class="sxs-lookup"><span data-stu-id="73eea-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="73eea-278">Les adaptateurs de connexion ont été supprimés de Kestrel et remplacés par Connection Middleware, qui est similaire à HTTP Middleware dans le pipeline ASP.NET Core, mais pour les connexions de niveau inférieur.</span><span class="sxs-lookup"><span data-stu-id="73eea-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="73eea-279">La couche de transport Kestrel a `Connections.Abstractions`été exposée comme une interface publique dans .</span><span class="sxs-lookup"><span data-stu-id="73eea-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="73eea-280">L’ambiguïté entre les en-têtes et les remorques a été résolue en déplaçant les en-têtes de fuite vers une nouvelle collection.</span><span class="sxs-lookup"><span data-stu-id="73eea-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="73eea-281">Synchrones I/O API, `HttpRequest.Body.Read`tels que , sont une source commune de famine de fil conduisant à des plantages d’applications.</span><span class="sxs-lookup"><span data-stu-id="73eea-281">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="73eea-282">En 3.0, `AllowSynchronousIO` est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="73eea-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="73eea-283">Pour plus d’informations, consultez <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="73eea-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="73eea-284">HTTP/2 activé par défaut</span><span class="sxs-lookup"><span data-stu-id="73eea-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="73eea-285">HTTP/2 est activé par défaut dans Kestrel pour les paramètres HTTPS.</span><span class="sxs-lookup"><span data-stu-id="73eea-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="73eea-286">LE support HTTP/2 pour IIS ou HTTP.sys est activé lorsqu’il est pris en charge par le système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="73eea-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="73eea-287">EventCounters sur demande</span><span class="sxs-lookup"><span data-stu-id="73eea-287">EventCounters on request</span></span>

<span data-ttu-id="73eea-288">L’Hosting EventSource, `Microsoft.AspNetCore.Hosting`, émet <xref:System.Diagnostics.Tracing.EventCounter> les nouveaux types suivants liés aux demandes entrantes :</span><span class="sxs-lookup"><span data-stu-id="73eea-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="73eea-289">Itinéraire de point de terminaison</span><span class="sxs-lookup"><span data-stu-id="73eea-289">Endpoint routing</span></span>

<span data-ttu-id="73eea-290">Endpoint Routing, qui permet aux cadres (par exemple, MVC) de bien fonctionner avec middleware, est amélioré :</span><span class="sxs-lookup"><span data-stu-id="73eea-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="73eea-291">L’ordre de middleware et les points de terminaison est configurable dans le pipeline de traitement de demande de `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="73eea-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="73eea-292">Les points de terminaison et les articles intermédiaires sont bien composés avec d’autres technologies ASP.NET basées sur le noyau, telles que les contrôles de santé.</span><span class="sxs-lookup"><span data-stu-id="73eea-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="73eea-293">Les points de terminaison peuvent mettre en œuvre une stratégie, comme le CORS ou l’autorisation, dans les articles intermédiaires et MVC.</span><span class="sxs-lookup"><span data-stu-id="73eea-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="73eea-294">Les filtres et les attributs peuvent être placés sur les méthodes dans les contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="73eea-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="73eea-295">Pour plus d’informations, consultez <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="73eea-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="73eea-296">Contrôles d'intégrité</span><span class="sxs-lookup"><span data-stu-id="73eea-296">Health Checks</span></span>

<span data-ttu-id="73eea-297">Les contrôles de santé utilisent le routage des points de terminaison avec l’hôte générique.</span><span class="sxs-lookup"><span data-stu-id="73eea-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="73eea-298">Dans `Startup.Configure`, `MapHealthChecks` appelez le constructeur de point de terminaison avec l’URL de point de terminaison ou le chemin relatif :</span><span class="sxs-lookup"><span data-stu-id="73eea-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="73eea-299">Les points de terminaison de contrôles de santé peuvent :</span><span class="sxs-lookup"><span data-stu-id="73eea-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="73eea-300">Spécifier un ou plusieurs hôtes/ports autorisés.</span><span class="sxs-lookup"><span data-stu-id="73eea-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="73eea-301">Exiger l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="73eea-301">Require authorization.</span></span>
* <span data-ttu-id="73eea-302">Exigez CORS.</span><span class="sxs-lookup"><span data-stu-id="73eea-302">Require CORS.</span></span>

<span data-ttu-id="73eea-303">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="73eea-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="73eea-304">Pipes sur HttpContext</span><span class="sxs-lookup"><span data-stu-id="73eea-304">Pipes on HttpContext</span></span>

<span data-ttu-id="73eea-305">Il est maintenant possible de lire le corps de <xref:System.IO.Pipelines> demande et d’écrire le corps de réponse à l’aide de l’API.</span><span class="sxs-lookup"><span data-stu-id="73eea-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="73eea-306">Les</span><span class="sxs-lookup"><span data-stu-id="73eea-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="73eea-307">`HttpRequest.BodyReader`propriété fournit <xref:System.IO.Pipelines.PipeReader> un qui peut être utilisé pour lire le corps de demande.</span><span class="sxs-lookup"><span data-stu-id="73eea-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="73eea-308">Les</span><span class="sxs-lookup"><span data-stu-id="73eea-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="73eea-309">`HttpResponse.BodyWriter`propriété fournit <xref:System.IO.Pipelines.PipeWriter> un qui peut être utilisé pour écrire le corps de réponse.</span><span class="sxs-lookup"><span data-stu-id="73eea-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="73eea-310">`HttpRequest.BodyReader`est un analogue `HttpRequest.Body` du flux.</span><span class="sxs-lookup"><span data-stu-id="73eea-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="73eea-311">`HttpResponse.BodyWriter`est un analogue `HttpResponse.Body` du flux.</span><span class="sxs-lookup"><span data-stu-id="73eea-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="73eea-312">Amélioration des rapports d’erreurs dans l’IIS</span><span class="sxs-lookup"><span data-stu-id="73eea-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="73eea-313">Les erreurs de démarrage lors de l’hébergement des applications ASP.NET Core dans IIS produisent maintenant des données diagnostiques plus riches.</span><span class="sxs-lookup"><span data-stu-id="73eea-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="73eea-314">Ces erreurs sont signalées au Journal d’événements Windows avec des traces de pile partout où cela est applicable.</span><span class="sxs-lookup"><span data-stu-id="73eea-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="73eea-315">En outre, tous les avertissements, erreurs et exceptions non gérées sont enregistrés dans le journal d’événements Windows.</span><span class="sxs-lookup"><span data-stu-id="73eea-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="73eea-316">Service aux travailleurs et travailleurS SDK</span><span class="sxs-lookup"><span data-stu-id="73eea-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="73eea-317">.NET Core 3.0 présente le nouveau modèle d’application Service aux travailleurs.</span><span class="sxs-lookup"><span data-stu-id="73eea-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="73eea-318">Ce modèle fournit un point de départ pour la rédaction de services à long terme dans .NET Core.</span><span class="sxs-lookup"><span data-stu-id="73eea-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="73eea-319">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="73eea-319">For more information, see:</span></span>

* [<span data-ttu-id="73eea-320">.NET Core Workers as Windows Services (en)</span><span class="sxs-lookup"><span data-stu-id="73eea-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="73eea-321">Améliorations de Têtes en avant Middleware</span><span class="sxs-lookup"><span data-stu-id="73eea-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="73eea-322">Dans les versions précédentes de <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> ASP.NET <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> Core, les appels et les posaient problème lorsqu’ils étaient déployés sur un Linux Azure ou derrière tout proxy inversé autre que l’IIS.</span><span class="sxs-lookup"><span data-stu-id="73eea-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="73eea-323">Le correctif pour les versions précédentes est documenté dans [Forward le système pour Linux et non-IIS mandataires inversés](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="73eea-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="73eea-324">Ce scénario est fixé dans ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="73eea-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="73eea-325">L’hôte permet le [Middleware Forwarded Headers](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) lorsque la variable de l’environnement `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est réglée à `true`.</span><span class="sxs-lookup"><span data-stu-id="73eea-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="73eea-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`est réglé `true` dans nos images de conteneurs.</span><span class="sxs-lookup"><span data-stu-id="73eea-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="73eea-327">Optimisation des performances</span><span class="sxs-lookup"><span data-stu-id="73eea-327">Performance improvements</span></span>

<span data-ttu-id="73eea-328">ASP.NET Core 3.0 comprend de nombreuses améliorations qui réduisent l’utilisation de la mémoire et améliorent le débit :</span><span class="sxs-lookup"><span data-stu-id="73eea-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="73eea-329">Réduction de l’utilisation de la mémoire lors de l’utilisation du conteneur d’injection de dépendance intégré pour les services de portée.</span><span class="sxs-lookup"><span data-stu-id="73eea-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="73eea-330">Réduction des allocations dans l’ensemble du cadre, y compris les scénarios de middleware et le routage.</span><span class="sxs-lookup"><span data-stu-id="73eea-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="73eea-331">Réduction de l’utilisation de la mémoire pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="73eea-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="73eea-332">Amélioration de la réduction de la mémoire et du débit pour les connexions HTTPS.</span><span class="sxs-lookup"><span data-stu-id="73eea-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="73eea-333">Nouveau sérialisateur JSON optimisé et entièrement asynchrone.</span><span class="sxs-lookup"><span data-stu-id="73eea-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="73eea-334">Réduction de l’utilisation de la mémoire et des améliorations de débit dans l’analyse de forme.</span><span class="sxs-lookup"><span data-stu-id="73eea-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="73eea-335">ASP.NET Core 3.0 ne fonctionne que sur .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="73eea-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="73eea-336">À partir de ASP.NET Core 3.0, le cadre NET n’est plus un cadre cible pris en charge.</span><span class="sxs-lookup"><span data-stu-id="73eea-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="73eea-337">Projets ciblant .NET Framework peut continuer d’une manière entièrement pris en charge en utilisant le [.NET Core 2.1 LTS version](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span><span class="sxs-lookup"><span data-stu-id="73eea-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="73eea-338">La plupart des paquets liés au noyau 2.1.x de base de ASP.NET seront pris en charge indéfiniment, au-delà de la période de trois ans de LTS pour .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="73eea-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="73eea-339">Pour plus d’informations sur la migration, consultez [Port votre code de .NET Framework à .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="73eea-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="73eea-340">Utiliser le cadre partagé ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73eea-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="73eea-341">Le cadre partagé ASP.NET Core 3.0, contenu dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), ne nécessite plus d’élément explicite `<PackageReference />` dans le fichier du projet.</span><span class="sxs-lookup"><span data-stu-id="73eea-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="73eea-342">Le cadre partagé est automatiquement `Microsoft.NET.Sdk.Web` référencé lors de l’utilisation du SDK dans le fichier du projet :</span><span class="sxs-lookup"><span data-stu-id="73eea-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="73eea-343">Assemblées retirées du cadre commun ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73eea-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="73eea-344">Les assemblages les plus notables retirés du cadre commun ASP.NET Core 3.0 sont les :</span><span class="sxs-lookup"><span data-stu-id="73eea-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="73eea-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span><span class="sxs-lookup"><span data-stu-id="73eea-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="73eea-346">Pour ajouter Json.NET à ASP.NET Core 3.0, voir [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="73eea-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="73eea-347">ASP.NET Core 3.0 introduit `System.Text.Json` pour la lecture et l’écriture JSON.</span><span class="sxs-lookup"><span data-stu-id="73eea-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="73eea-348">Pour plus d’informations, voir [Nouvelle sérialisation JSON](#new-json-serialization) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="73eea-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="73eea-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="73eea-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="73eea-350">Pour une liste complète des assemblages supprimés du cadre partagé, voir [les assemblages supprimés de Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="73eea-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="73eea-351">Pour plus d’informations sur la motivation de ce changement, voir [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="73eea-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 