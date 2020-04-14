---
title: Utilisez le protocole SignalR MessagePack Hub pour ASP.NET Core
author: bradygaster
description: Ajouter le protocole MessagePack Hub SignalRà ASP.NET Core .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277234"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="0c3d7-103">Utilisez le protocole SignalR MessagePack Hub pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c3d7-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0c3d7-104">Cet article suppose que le lecteur est familier avec les sujets abordés dans [Get Started](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="0c3d7-105">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="0c3d7-105">What is MessagePack?</span></span>

<span data-ttu-id="0c3d7-106">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="0c3d7-107">Il est utile lorsque la performance et la bande passante sont une préoccupation, car il crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="0c3d7-108">Les messages binaires sont illisibles lorsque vous regardez les traces et les journaux réseau à moins que les octets ne soient passés par un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="0c3d7-109">a intégré le support intégré pour le format MessagePack et fournit des API pour le client et le serveur à utiliser.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="0c3d7-110">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="0c3d7-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="0c3d7-111">Pour activer le protocole MessagePack Hub `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` sur le serveur, installez le paquet dans votre application.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="0c3d7-112">Dans `Startup.ConfigureServices` la méthode, `AddMessagePackProtocol` `AddSignalR` ajouter à l’appel pour activer le support MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="0c3d7-113">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-113">JSON is enabled by default.</span></span> <span data-ttu-id="0c3d7-114">L’ajout de MessagePack permet de prendre en charge les clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="0c3d7-115">Pour personnaliser la façon dont MessagePack formatera vos données, `AddMessagePackProtocol` prend un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="0c3d7-116">Dans ce délégué, la `SerializerOptions` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="0c3d7-117">Pour plus d’informations sur le fonctionnement des résolveurs, visitez la bibliothèque MessagePack à [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="0c3d7-118">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir comment ils doivent être manipulés.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="0c3d7-119">Nous recommandons fortement d’examiner [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="0c3d7-120">Par exemple, `.WithSecurity(MessagePackSecurity.UntrustedData)` appeler lors `SerializerOptions`du remplacement de la .</span><span class="sxs-lookup"><span data-stu-id="0c3d7-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="0c3d7-121">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="0c3d7-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="0c3d7-122">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="0c3d7-123">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="0c3d7-124">L’ajout du support MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="0c3d7-125">Client .NET</span><span class="sxs-lookup"><span data-stu-id="0c3d7-125">.NET client</span></span>

<span data-ttu-id="0c3d7-126">Pour activer MessagePack dans le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` client .NET, installez le paquet et appelez `AddMessagePackProtocol` . `HubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="0c3d7-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="0c3d7-127">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer des options tout comme le serveur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="0c3d7-128">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="0c3d7-128">JavaScript client</span></span>

<span data-ttu-id="0c3d7-129">MessagePack support pour le client JavaScript est fourni par le [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) paquet npm.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="0c3d7-130">Installer le paquet en exécutant la commande suivante dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="0c3d7-131">Après l’installation du paquet npm, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en faisant référence au fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="0c3d7-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="0c3d7-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="0c3d7-133">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="0c3d7-134">Utilisez `<script>` une balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="0c3d7-135">La bibliothèque peut être trouvée à *node_modules’msgpack5'dist’msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="0c3d7-136">Lors de `<script>` l’utilisation de l’élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="0c3d7-137">Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lorsque vous essayez de se connecter à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="0c3d7-138">*signalr.js* est également nécessaire avant *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="0c3d7-139">Ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` à `HubConnectionBuilder` la configuration du client pour utiliser le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="0c3d7-140">Pour le moment, il n’existe pas d’options de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="0c3d7-141">Les bizarreries messagePack</span><span class="sxs-lookup"><span data-stu-id="0c3d7-141">MessagePack quirks</span></span>

<span data-ttu-id="0c3d7-142">Il y a quelques problèmes à connaître lors de l’utilisation du protocole Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="0c3d7-143">MessagePack est sensible aux cas</span><span class="sxs-lookup"><span data-stu-id="0c3d7-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="0c3d7-144">Le protocole MessagePack est sensible aux cas.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="0c3d7-145">Par exemple, considérez la classe C suivante :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="0c3d7-146">Lors de l’envoi du client `PascalCased` JavaScript, vous devez utiliser les noms de propriété, puisque le boîtier doit correspondre à la classe C exactement.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="0c3d7-147">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="0c3d7-148">L’utilisation de `camelCased` noms ne se lie pas correctement à la classe C.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="0c3d7-149">Vous pouvez contourner ce `Key` sujet en utilisant l’attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="0c3d7-150">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="0c3d7-151">DateTime.Kind n’est pas conservé lors de la sérialisation/deserialisation</span><span class="sxs-lookup"><span data-stu-id="0c3d7-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="0c3d7-152">Le protocole MessagePack ne fournit pas un `Kind` moyen `DateTime`d’encoder la valeur d’un .</span><span class="sxs-lookup"><span data-stu-id="0c3d7-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="0c3d7-153">Par conséquent, lors de la désétération d’une date, le protocole `DateTime.Kind` `DateTimeKind.Local` MessagePack Hub se convertira au format UTC si l’est autrement, il ne touchera pas l’heure et la passera telle quel.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="0c3d7-154">Si vous travaillez `DateTime` avec des valeurs, nous vous recommandons de vous convertir à UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="0c3d7-155">Convertissez-les de l’UTC à l’heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="0c3d7-156">DateTime.MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="0c3d7-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="0c3d7-157">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilisée par le client `timestamp96` JavaScript ne prend pas en charge le type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="0c3d7-158">Ce type est utilisé pour coder des valeurs de date très grandes (soit très tôt dans le passé ou très loin à l’avenir).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="0c3d7-159">La valeur `DateTime.MinValue` `January 1, 0001`de est , qui doit `timestamp96` être codé dans une valeur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="0c3d7-160">Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="0c3d7-161">Lorsque `DateTime.MinValue` le client JavaScript est reçu, l’erreur suivante est lancée :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="0c3d7-162">Habituellement, `DateTime.MinValue` est utilisé pour coder `null` un "manquant" ou la valeur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="0c3d7-163">Si vous devez coder cette valeur dans MessagePack, utilisez une `DateTime` valeur nulle (`DateTime?`) ou codez une valeur distincte `bool` indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="0c3d7-164">Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="0c3d7-165">MessagePack support dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="0c3d7-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="0c3d7-166">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="0c3d7-167">En conséquence, il n’est pas pris en charge par défaut sur les environnements qui utilisent "à l’avance" compilation (comme Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="0c3d7-168">Il est possible d’utiliser MessagePack dans ces environnements en « pré-générant » le code sérialisateur/déséialiseur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="0c3d7-169">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="0c3d7-170">Une fois que vous avez pré-généré les sérialisateurs, vous pouvez les enregistrer à l’aide du délégué de configuration passé à `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="0c3d7-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="0c3d7-171">Les contrôles de type sont plus stricts dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="0c3d7-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="0c3d7-172">Le protocole JSON Hub effectuera des conversions de type pendant la désétération.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="0c3d7-173">Par exemple, si l’objet entrant a une`{ foo: 42 }`valeur de propriété qui est un `string`nombre ( ) mais la propriété sur la classe .NET est de type, la valeur sera convertie.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="0c3d7-174">Cependant, MessagePack n’effectue pas cette conversion et lancera une exception qui peut être vu dans les journaux côté serveur (et dans la console):</span><span class="sxs-lookup"><span data-stu-id="0c3d7-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="0c3d7-175">Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0c3d7-176">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="0c3d7-176">Related resources</span></span>

* [<span data-ttu-id="0c3d7-177">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="0c3d7-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0c3d7-178">.NET client</span><span class="sxs-lookup"><span data-stu-id="0c3d7-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0c3d7-179">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="0c3d7-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="0c3d7-180">Cet article suppose que le lecteur est familier avec les sujets abordés dans [Get Started](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="0c3d7-181">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="0c3d7-181">What is MessagePack?</span></span>

<span data-ttu-id="0c3d7-182">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="0c3d7-183">Il est utile lorsque la performance et la bande passante sont une préoccupation, car il crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="0c3d7-184">Les messages binaires sont illisibles lorsque vous regardez les traces et les journaux réseau à moins que les octets ne soient passés par un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="0c3d7-185">a intégré le support intégré pour le format MessagePack, et fournit des API pour le client et le serveur à utiliser.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="0c3d7-186">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="0c3d7-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="0c3d7-187">Pour activer le protocole MessagePack Hub `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` sur le serveur, installez le paquet dans votre application.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="0c3d7-188">Dans `Startup.ConfigureServices` la méthode, `AddMessagePackProtocol` `AddSignalR` ajouter à l’appel pour activer le support MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="0c3d7-189">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-189">JSON is enabled by default.</span></span> <span data-ttu-id="0c3d7-190">L’ajout de MessagePack permet de prendre en charge les clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="0c3d7-191">Pour personnaliser la façon dont MessagePack formatera vos données, `AddMessagePackProtocol` prend un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="0c3d7-192">Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="0c3d7-193">Pour plus d’informations sur le fonctionnement des résolveurs, visitez la bibliothèque MessagePack à [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="0c3d7-194">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir comment ils doivent être manipulés.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="0c3d7-195">Nous recommandons fortement d’examiner [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="0c3d7-196">Par exemple, `MessagePackSecurity.Active` définir la `MessagePackSecurity.UntrustedData`propriété statique à .</span><span class="sxs-lookup"><span data-stu-id="0c3d7-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="0c3d7-197">Réglage `MessagePackSecurity.Active` des besoins en installation manuelle d’une [version 1.9.x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="0c3d7-198">Installation `MessagePack` 1.9.x mises à SignalR niveau de la version utilise.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="0c3d7-199">Quand `MessagePackSecurity.Active` n’est `MessagePackSecurity.UntrustedData`pas configuré à , un client malveillant peut causer un déni de service.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="0c3d7-200">Définissez, `MessagePackSecurity.Active` `Program.Main`comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="0c3d7-201">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="0c3d7-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="0c3d7-202">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="0c3d7-203">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="0c3d7-204">L’ajout du support MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="0c3d7-205">Client .NET</span><span class="sxs-lookup"><span data-stu-id="0c3d7-205">.NET client</span></span>

<span data-ttu-id="0c3d7-206">Pour activer MessagePack dans le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` client .NET, installez le paquet et appelez `AddMessagePackProtocol` . `HubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="0c3d7-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="0c3d7-207">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer des options tout comme le serveur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="0c3d7-208">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="0c3d7-208">JavaScript client</span></span>

<span data-ttu-id="0c3d7-209">MessagePack support pour le client JavaScript est fourni par le [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) paquet npm.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="0c3d7-210">Installer le paquet en exécutant la commande suivante dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="0c3d7-211">Après l’installation du paquet npm, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en faisant référence au fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="0c3d7-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="0c3d7-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="0c3d7-213">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="0c3d7-214">Utilisez `<script>` une balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="0c3d7-215">La bibliothèque peut être trouvée à *node_modules’msgpack5'dist’msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="0c3d7-216">Lors de `<script>` l’utilisation de l’élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="0c3d7-217">Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lorsque vous essayez de se connecter à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="0c3d7-218">*signalr.js* est également nécessaire avant *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="0c3d7-219">Ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` à `HubConnectionBuilder` la configuration du client pour utiliser le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="0c3d7-220">Pour le moment, il n’existe pas d’options de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="0c3d7-221">Les bizarreries messagePack</span><span class="sxs-lookup"><span data-stu-id="0c3d7-221">MessagePack quirks</span></span>

<span data-ttu-id="0c3d7-222">Il y a quelques problèmes à connaître lors de l’utilisation du protocole Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="0c3d7-223">MessagePack est sensible aux cas</span><span class="sxs-lookup"><span data-stu-id="0c3d7-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="0c3d7-224">Le protocole MessagePack est sensible aux cas.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="0c3d7-225">Par exemple, considérez la classe C suivante :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="0c3d7-226">Lors de l’envoi du client `PascalCased` JavaScript, vous devez utiliser les noms de propriété, puisque le boîtier doit correspondre à la classe C exactement.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="0c3d7-227">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="0c3d7-228">L’utilisation de `camelCased` noms ne se lie pas correctement à la classe C.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="0c3d7-229">Vous pouvez contourner ce `Key` sujet en utilisant l’attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="0c3d7-230">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="0c3d7-231">DateTime.Kind n’est pas conservé lors de la sérialisation/deserialisation</span><span class="sxs-lookup"><span data-stu-id="0c3d7-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="0c3d7-232">Le protocole MessagePack ne fournit pas un `Kind` moyen `DateTime`d’encoder la valeur d’un .</span><span class="sxs-lookup"><span data-stu-id="0c3d7-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="0c3d7-233">Par conséquent, lors de la désétération d’une date, le protocole MessagePack Hub suppose que la date d’entrée est en format UTC.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="0c3d7-234">Si vous travaillez `DateTime` avec des valeurs à l’heure locale, nous vous recommandons de vous convertir à UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="0c3d7-235">Convertissez-les de l’UTC à l’heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="0c3d7-236">Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="0c3d7-237">DateTime.MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="0c3d7-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="0c3d7-238">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilisée par le client `timestamp96` JavaScript ne prend pas en charge le type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="0c3d7-239">Ce type est utilisé pour coder des valeurs de date très grandes (soit très tôt dans le passé ou très loin à l’avenir).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="0c3d7-240">La valeur `DateTime.MinValue` `January 1, 0001`de est , qui doit `timestamp96` être codé dans une valeur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="0c3d7-241">Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="0c3d7-242">Lorsque `DateTime.MinValue` le client JavaScript est reçu, l’erreur suivante est lancée :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="0c3d7-243">Habituellement, `DateTime.MinValue` est utilisé pour coder `null` un "manquant" ou la valeur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="0c3d7-244">Si vous devez coder cette valeur dans MessagePack, utilisez une `DateTime` valeur nulle (`DateTime?`) ou codez une valeur distincte `bool` indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="0c3d7-245">Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="0c3d7-246">MessagePack support dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="0c3d7-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="0c3d7-247">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="0c3d7-248">En conséquence, il n’est pas pris en charge par défaut sur les environnements qui utilisent "à l’avance" compilation (comme Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="0c3d7-249">Il est possible d’utiliser MessagePack dans ces environnements en « pré-générant » le code sérialisateur/déséialiseur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="0c3d7-250">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="0c3d7-251">Une fois que vous avez pré-généré les sérialisateurs, vous pouvez les enregistrer à l’aide du délégué de configuration passé à `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="0c3d7-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="0c3d7-252">Les contrôles de type sont plus stricts dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="0c3d7-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="0c3d7-253">Le protocole JSON Hub effectuera des conversions de type pendant la désétération.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="0c3d7-254">Par exemple, si l’objet entrant a une`{ foo: 42 }`valeur de propriété qui est un `string`nombre ( ) mais la propriété sur la classe .NET est de type, la valeur sera convertie.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="0c3d7-255">Cependant, MessagePack n’effectue pas cette conversion et lancera une exception qui peut être vu dans les journaux côté serveur (et dans la console):</span><span class="sxs-lookup"><span data-stu-id="0c3d7-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="0c3d7-256">Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0c3d7-257">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="0c3d7-257">Related resources</span></span>

* [<span data-ttu-id="0c3d7-258">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="0c3d7-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0c3d7-259">.NET client</span><span class="sxs-lookup"><span data-stu-id="0c3d7-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0c3d7-260">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="0c3d7-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0c3d7-261">Cet article suppose que le lecteur est familier avec les sujets abordés dans [Get Started](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="0c3d7-262">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="0c3d7-262">What is MessagePack?</span></span>

<span data-ttu-id="0c3d7-263">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="0c3d7-264">Il est utile lorsque la performance et la bande passante sont une préoccupation, car il crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="0c3d7-265">Les messages binaires sont illisibles lorsque vous regardez les traces et les journaux réseau à moins que les octets ne soient passés par un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="0c3d7-266">a intégré le support intégré pour le format MessagePack, et fournit des API pour le client et le serveur à utiliser.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="0c3d7-267">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="0c3d7-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="0c3d7-268">Pour activer le protocole MessagePack Hub `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` sur le serveur, installez le paquet dans votre application.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="0c3d7-269">Dans `Startup.ConfigureServices` la méthode, `AddMessagePackProtocol` `AddSignalR` ajouter à l’appel pour activer le support MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="0c3d7-270">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-270">JSON is enabled by default.</span></span> <span data-ttu-id="0c3d7-271">L’ajout de MessagePack permet de prendre en charge les clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="0c3d7-272">Pour personnaliser la façon dont MessagePack formatera vos données, `AddMessagePackProtocol` prend un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="0c3d7-273">Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="0c3d7-274">Pour plus d’informations sur le fonctionnement des résolveurs, visitez la bibliothèque MessagePack à [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="0c3d7-275">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir comment ils doivent être manipulés.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="0c3d7-276">Nous recommandons fortement d’examiner [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="0c3d7-277">Par exemple, `MessagePackSecurity.Active` définir la `MessagePackSecurity.UntrustedData`propriété statique à .</span><span class="sxs-lookup"><span data-stu-id="0c3d7-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="0c3d7-278">Réglage `MessagePackSecurity.Active` des besoins en installation manuelle d’une [version 1.9.x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="0c3d7-279">Installation `MessagePack` 1.9.x mises à SignalR niveau de la version utilise.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="0c3d7-280">Quand `MessagePackSecurity.Active` n’est `MessagePackSecurity.UntrustedData`pas configuré à , un client malveillant peut causer un déni de service.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="0c3d7-281">Définissez, `MessagePackSecurity.Active` `Program.Main`comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="0c3d7-282">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="0c3d7-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="0c3d7-283">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="0c3d7-284">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="0c3d7-285">L’ajout du support MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="0c3d7-286">Client .NET</span><span class="sxs-lookup"><span data-stu-id="0c3d7-286">.NET client</span></span>

<span data-ttu-id="0c3d7-287">Pour activer MessagePack dans le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` client .NET, installez le paquet et appelez `AddMessagePackProtocol` . `HubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="0c3d7-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="0c3d7-288">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer des options tout comme le serveur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="0c3d7-289">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="0c3d7-289">JavaScript client</span></span>

<span data-ttu-id="0c3d7-290">MessagePack support pour le client JavaScript est fourni par le [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) paquet npm.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="0c3d7-291">Installer le paquet en exécutant la commande suivante dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="0c3d7-292">Après l’installation du paquet npm, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en faisant référence au fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="0c3d7-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="0c3d7-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="0c3d7-294">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="0c3d7-295">Utilisez `<script>` une balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="0c3d7-296">La bibliothèque peut être trouvée à *node_modules’msgpack5'dist’msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="0c3d7-297">Lors de `<script>` l’utilisation de l’élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="0c3d7-298">Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lorsque vous essayez de se connecter à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="0c3d7-299">*signalr.js* est également nécessaire avant *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="0c3d7-300">Ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` à `HubConnectionBuilder` la configuration du client pour utiliser le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="0c3d7-301">Pour le moment, il n’existe pas d’options de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="0c3d7-302">Les bizarreries messagePack</span><span class="sxs-lookup"><span data-stu-id="0c3d7-302">MessagePack quirks</span></span>

<span data-ttu-id="0c3d7-303">Il y a quelques problèmes à connaître lors de l’utilisation du protocole Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="0c3d7-304">MessagePack est sensible aux cas</span><span class="sxs-lookup"><span data-stu-id="0c3d7-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="0c3d7-305">Le protocole MessagePack est sensible aux cas.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="0c3d7-306">Par exemple, considérez la classe C suivante :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="0c3d7-307">Lors de l’envoi du client `PascalCased` JavaScript, vous devez utiliser les noms de propriété, puisque le boîtier doit correspondre à la classe C exactement.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="0c3d7-308">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="0c3d7-309">L’utilisation de `camelCased` noms ne se lie pas correctement à la classe C.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="0c3d7-310">Vous pouvez contourner ce `Key` sujet en utilisant l’attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="0c3d7-311">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="0c3d7-312">DateTime.Kind n’est pas conservé lors de la sérialisation/deserialisation</span><span class="sxs-lookup"><span data-stu-id="0c3d7-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="0c3d7-313">Le protocole MessagePack ne fournit pas un `Kind` moyen `DateTime`d’encoder la valeur d’un .</span><span class="sxs-lookup"><span data-stu-id="0c3d7-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="0c3d7-314">Par conséquent, lors de la désétération d’une date, le protocole MessagePack Hub suppose que la date d’entrée est en format UTC.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="0c3d7-315">Si vous travaillez `DateTime` avec des valeurs à l’heure locale, nous vous recommandons de vous convertir à UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="0c3d7-316">Convertissez-les de l’UTC à l’heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="0c3d7-317">Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="0c3d7-318">DateTime.MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="0c3d7-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="0c3d7-319">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilisée par le client `timestamp96` JavaScript ne prend pas en charge le type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="0c3d7-320">Ce type est utilisé pour coder des valeurs de date très grandes (soit très tôt dans le passé ou très loin à l’avenir).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="0c3d7-321">La valeur `DateTime.MinValue` `January 1, 0001` de qui doit être `timestamp96` codée dans une valeur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="0c3d7-322">Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="0c3d7-323">Lorsque `DateTime.MinValue` le client JavaScript est reçu, l’erreur suivante est lancée :</span><span class="sxs-lookup"><span data-stu-id="0c3d7-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="0c3d7-324">Habituellement, `DateTime.MinValue` est utilisé pour coder `null` un "manquant" ou la valeur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="0c3d7-325">Si vous devez coder cette valeur dans MessagePack, utilisez une `DateTime` valeur nulle (`DateTime?`) ou codez une valeur distincte `bool` indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="0c3d7-326">Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="0c3d7-327">MessagePack support dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="0c3d7-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="0c3d7-328">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="0c3d7-329">En conséquence, il n’est pas pris en charge par défaut sur les environnements qui utilisent "à l’avance" compilation (comme Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="0c3d7-330">Il est possible d’utiliser MessagePack dans ces environnements en « pré-générant » le code sérialisateur/déséialiseur.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="0c3d7-331">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="0c3d7-332">Une fois que vous avez pré-généré les sérialisateurs, vous pouvez les enregistrer à l’aide du délégué de configuration passé à `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="0c3d7-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="0c3d7-333">Les contrôles de type sont plus stricts dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="0c3d7-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="0c3d7-334">Le protocole JSON Hub effectuera des conversions de type pendant la désétération.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="0c3d7-335">Par exemple, si l’objet entrant a une`{ foo: 42 }`valeur de propriété qui est un `string`nombre ( ) mais la propriété sur la classe .NET est de type, la valeur sera convertie.</span><span class="sxs-lookup"><span data-stu-id="0c3d7-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="0c3d7-336">Cependant, MessagePack n’effectue pas cette conversion et lancera une exception qui peut être vu dans les journaux côté serveur (et dans la console):</span><span class="sxs-lookup"><span data-stu-id="0c3d7-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="0c3d7-337">Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="0c3d7-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0c3d7-338">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="0c3d7-338">Related resources</span></span>

* [<span data-ttu-id="0c3d7-339">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="0c3d7-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0c3d7-340">.NET client</span><span class="sxs-lookup"><span data-stu-id="0c3d7-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0c3d7-341">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="0c3d7-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
