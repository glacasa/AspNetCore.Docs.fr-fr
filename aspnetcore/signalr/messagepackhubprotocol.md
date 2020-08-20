---
title: Utiliser le protocole MessagePack Hub dans SignalR pour ASP.net Core
author: bradygaster
description: Ajoutez le protocole MessagePack Hub à ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: ab9bd11e37182f5b24db5595d5d050f4cc0e32da
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626647"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="2448d-103">Utiliser le protocole MessagePack Hub dans SignalR pour ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="2448d-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2448d-104">Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="2448d-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2448d-105">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="2448d-105">What is MessagePack?</span></span>

<span data-ttu-id="2448d-106">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="2448d-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="2448d-107">Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2448d-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2448d-108">Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2448d-109">SignalR dispose d’une prise en charge intégrée du format MessagePack et fournit des API pour le client et le serveur à utiliser.</span><span class="sxs-lookup"><span data-stu-id="2448d-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2448d-110">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="2448d-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="2448d-111">Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application.</span><span class="sxs-lookup"><span data-stu-id="2448d-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2448d-112">Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="2448d-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2448d-113">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="2448d-113">JSON is enabled by default.</span></span> <span data-ttu-id="2448d-114">L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2448d-115">Pour personnaliser la façon dont MessagePack met en forme vos données, `AddMessagePackProtocol` utilise un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="2448d-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2448d-116">Dans ce délégué, la `SerializerOptions` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2448d-117">Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2448d-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2448d-118">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.</span><span class="sxs-lookup"><span data-stu-id="2448d-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="2448d-119">Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="2448d-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="2448d-120">Par exemple, appeler `.WithSecurity(MessagePackSecurity.UntrustedData)` lors du remplacement de `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="2448d-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2448d-121">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="2448d-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2448d-122">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="2448d-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2448d-123">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="2448d-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="2448d-124">L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="2448d-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2448d-125">Client .NET</span><span class="sxs-lookup"><span data-stu-id="2448d-125">.NET client</span></span>

<span data-ttu-id="2448d-126">Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2448d-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2448d-127">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.</span><span class="sxs-lookup"><span data-stu-id="2448d-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2448d-128">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="2448d-128">JavaScript client</span></span>

<span data-ttu-id="2448d-129">La prise en charge de MessagePack pour le client JavaScript est fournie par le [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) package NPM.</span><span class="sxs-lookup"><span data-stu-id="2448d-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="2448d-130">Installez le package en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="2448d-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="2448d-131">Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="2448d-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="2448d-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="2448d-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="2448d-133">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="2448d-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2448d-134">Utilisez une `<script>` balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="2448d-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2448d-135">La bibliothèque se trouve sur *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2448d-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2448d-136">Lors de l’utilisation de l' `<script>` élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="2448d-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2448d-137">Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lors de la tentative de connexion à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2448d-138">*signalr.js* est également requis avant *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2448d-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2448d-139">L’ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` de à l' `HubConnectionBuilder` configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="2448d-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2448d-140">À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2448d-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="2448d-141">MessagePack excentriques</span><span class="sxs-lookup"><span data-stu-id="2448d-141">MessagePack quirks</span></span>

<span data-ttu-id="2448d-142">Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="2448d-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="2448d-143">MessagePack respecte la casse</span><span class="sxs-lookup"><span data-stu-id="2448d-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="2448d-144">Le protocole MessagePack respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="2448d-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="2448d-145">Par exemple, considérez la classe C# suivante :</span><span class="sxs-lookup"><span data-stu-id="2448d-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="2448d-146">Lors de l’envoi à partir du client JavaScript, vous devez utiliser des `PascalCased` noms de propriété, car la casse doit correspondre exactement à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="2448d-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="2448d-147">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="2448d-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="2448d-148">L’utilisation `camelCased` de noms n’est pas correctement liée à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="2448d-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="2448d-149">Vous pouvez contourner ce contournement en utilisant l' `Key` attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="2448d-150">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="2448d-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="2448d-151">DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation</span><span class="sxs-lookup"><span data-stu-id="2448d-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="2448d-152">Le protocole MessagePack ne fournit pas de méthode pour encoder la `Kind` valeur d’un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="2448d-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="2448d-153">Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub est converti au format UTC si le `DateTime.Kind` n’est pas le cas, `DateTimeKind.Local` et il ne peut pas toucher l’heure et le passer tel quel.</span><span class="sxs-lookup"><span data-stu-id="2448d-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="2448d-154">Si vous utilisez des `DateTime` valeurs, nous vous recommandons de convertir au format UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="2448d-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="2448d-155">Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="2448d-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="2448d-156">DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="2448d-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="2448d-157">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas en charge le `timestamp96` type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="2448d-158">Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur).</span><span class="sxs-lookup"><span data-stu-id="2448d-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="2448d-159">La valeur de `DateTime.MinValue` est `January 1, 0001` , qui doit être encodée dans une `timestamp96` valeur.</span><span class="sxs-lookup"><span data-stu-id="2448d-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="2448d-160">Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="2448d-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="2448d-161">Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :</span><span class="sxs-lookup"><span data-stu-id="2448d-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="2448d-162">Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « manquante » ou `null` .</span><span class="sxs-lookup"><span data-stu-id="2448d-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="2448d-163">Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable ( `DateTime?` ) ou encodez une `bool` valeur distincte indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="2448d-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="2448d-164">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="2448d-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="2448d-165">Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="2448d-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="2448d-166">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="2448d-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="2448d-167">Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="2448d-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="2448d-168">Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur.</span><span class="sxs-lookup"><span data-stu-id="2448d-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="2448d-169">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="2448d-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="2448d-170">Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide du délégué de configuration passé à `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="2448d-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="2448d-171">Les vérifications de type sont plus strictes dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="2448d-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="2448d-172">Le protocole JSON Hub effectue des conversions de type pendant la désérialisation.</span><span class="sxs-lookup"><span data-stu-id="2448d-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="2448d-173">Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre ( `{ foo: 42 }` ) mais que la propriété de la classe .net est de type `string` , la valeur est convertie.</span><span class="sxs-lookup"><span data-stu-id="2448d-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="2448d-174">Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :</span><span class="sxs-lookup"><span data-stu-id="2448d-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="2448d-175">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="2448d-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="2448d-176">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="2448d-176">Related resources</span></span>

* [<span data-ttu-id="2448d-177">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="2448d-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2448d-178">Client .NET</span><span class="sxs-lookup"><span data-stu-id="2448d-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2448d-179">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="2448d-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="2448d-180">Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="2448d-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2448d-181">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="2448d-181">What is MessagePack?</span></span>

<span data-ttu-id="2448d-182">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="2448d-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="2448d-183">Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2448d-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2448d-184">Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2448d-185">SignalR dispose d’une prise en charge intégrée du format MessagePack et fournit des API que le client et le serveur doivent utiliser.</span><span class="sxs-lookup"><span data-stu-id="2448d-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2448d-186">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="2448d-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="2448d-187">Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application.</span><span class="sxs-lookup"><span data-stu-id="2448d-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2448d-188">Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="2448d-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2448d-189">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="2448d-189">JSON is enabled by default.</span></span> <span data-ttu-id="2448d-190">L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2448d-191">Pour personnaliser la façon dont MessagePack met en forme vos données, `AddMessagePackProtocol` utilise un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="2448d-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2448d-192">Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2448d-193">Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2448d-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2448d-194">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.</span><span class="sxs-lookup"><span data-stu-id="2448d-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="2448d-195">Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="2448d-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="2448d-196">Par exemple, en affectant `MessagePackSecurity.Active` à la propriété statique la valeur `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="2448d-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="2448d-197">La définition `MessagePackSecurity.Active` de requiert l’installation manuelle [d’une version 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="2448d-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="2448d-198">`MessagePack`L’installation de la version 1.9. x met à niveau la version SignalR .</span><span class="sxs-lookup"><span data-stu-id="2448d-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="2448d-199">Lorsque `MessagePackSecurity.Active` n’a pas `MessagePackSecurity.UntrustedData` la valeur, un client malveillant peut provoquer un déni de service.</span><span class="sxs-lookup"><span data-stu-id="2448d-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="2448d-200">Définissez `MessagePackSecurity.Active` dans `Program.Main` , comme illustré dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2448d-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2448d-201">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="2448d-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2448d-202">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="2448d-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2448d-203">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="2448d-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="2448d-204">L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="2448d-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2448d-205">Client .NET</span><span class="sxs-lookup"><span data-stu-id="2448d-205">.NET client</span></span>

<span data-ttu-id="2448d-206">Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2448d-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2448d-207">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.</span><span class="sxs-lookup"><span data-stu-id="2448d-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2448d-208">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="2448d-208">JavaScript client</span></span>

<span data-ttu-id="2448d-209">La prise en charge de MessagePack pour le client JavaScript est fournie par le [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) package NPM.</span><span class="sxs-lookup"><span data-stu-id="2448d-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="2448d-210">Installez le package en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="2448d-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="2448d-211">Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="2448d-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="2448d-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="2448d-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="2448d-213">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="2448d-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2448d-214">Utilisez une `<script>` balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="2448d-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2448d-215">La bibliothèque se trouve sur *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2448d-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2448d-216">Lors de l’utilisation de l' `<script>` élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="2448d-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2448d-217">Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lors de la tentative de connexion à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2448d-218">*signalr.js* est également requis avant *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2448d-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2448d-219">L’ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` de à l' `HubConnectionBuilder` configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="2448d-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2448d-220">À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2448d-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="2448d-221">MessagePack excentriques</span><span class="sxs-lookup"><span data-stu-id="2448d-221">MessagePack quirks</span></span>

<span data-ttu-id="2448d-222">Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="2448d-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="2448d-223">MessagePack respecte la casse</span><span class="sxs-lookup"><span data-stu-id="2448d-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="2448d-224">Le protocole MessagePack respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="2448d-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="2448d-225">Par exemple, considérez la classe C# suivante :</span><span class="sxs-lookup"><span data-stu-id="2448d-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="2448d-226">Lors de l’envoi à partir du client JavaScript, vous devez utiliser des `PascalCased` noms de propriété, car la casse doit correspondre exactement à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="2448d-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="2448d-227">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="2448d-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="2448d-228">L’utilisation `camelCased` de noms n’est pas correctement liée à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="2448d-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="2448d-229">Vous pouvez contourner ce contournement en utilisant l' `Key` attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="2448d-230">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="2448d-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="2448d-231">DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation</span><span class="sxs-lookup"><span data-stu-id="2448d-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="2448d-232">Le protocole MessagePack ne fournit pas de méthode pour encoder la `Kind` valeur d’un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="2448d-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="2448d-233">Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub part du principe que la date d’entrée est au format UTC.</span><span class="sxs-lookup"><span data-stu-id="2448d-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="2448d-234">Si vous utilisez des `DateTime` valeurs en heure locale, nous vous recommandons de convertir au format UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="2448d-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="2448d-235">Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="2448d-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="2448d-236">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="2448d-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="2448d-237">DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="2448d-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="2448d-238">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas en charge le `timestamp96` type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="2448d-239">Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur).</span><span class="sxs-lookup"><span data-stu-id="2448d-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="2448d-240">La valeur de `DateTime.MinValue` est `January 1, 0001` , qui doit être encodée dans une `timestamp96` valeur.</span><span class="sxs-lookup"><span data-stu-id="2448d-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="2448d-241">Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="2448d-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="2448d-242">Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :</span><span class="sxs-lookup"><span data-stu-id="2448d-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="2448d-243">Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « manquante » ou `null` .</span><span class="sxs-lookup"><span data-stu-id="2448d-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="2448d-244">Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable ( `DateTime?` ) ou encodez une `bool` valeur distincte indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="2448d-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="2448d-245">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="2448d-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="2448d-246">Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="2448d-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="2448d-247">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="2448d-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="2448d-248">Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="2448d-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="2448d-249">Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur.</span><span class="sxs-lookup"><span data-stu-id="2448d-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="2448d-250">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="2448d-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="2448d-251">Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide du délégué de configuration passé à `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="2448d-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="2448d-252">Les vérifications de type sont plus strictes dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="2448d-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="2448d-253">Le protocole JSON Hub effectue des conversions de type pendant la désérialisation.</span><span class="sxs-lookup"><span data-stu-id="2448d-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="2448d-254">Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre ( `{ foo: 42 }` ) mais que la propriété de la classe .net est de type `string` , la valeur est convertie.</span><span class="sxs-lookup"><span data-stu-id="2448d-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="2448d-255">Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :</span><span class="sxs-lookup"><span data-stu-id="2448d-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="2448d-256">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="2448d-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="2448d-257">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="2448d-257">Related resources</span></span>

* [<span data-ttu-id="2448d-258">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="2448d-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2448d-259">Client .NET</span><span class="sxs-lookup"><span data-stu-id="2448d-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2448d-260">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="2448d-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2448d-261">Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="2448d-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2448d-262">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="2448d-262">What is MessagePack?</span></span>

<span data-ttu-id="2448d-263">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="2448d-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="2448d-264">Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2448d-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2448d-265">Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2448d-266">SignalR dispose d’une prise en charge intégrée du format MessagePack et fournit des API que le client et le serveur doivent utiliser.</span><span class="sxs-lookup"><span data-stu-id="2448d-266">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2448d-267">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="2448d-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="2448d-268">Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application.</span><span class="sxs-lookup"><span data-stu-id="2448d-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2448d-269">Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="2448d-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2448d-270">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="2448d-270">JSON is enabled by default.</span></span> <span data-ttu-id="2448d-271">L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2448d-272">Pour personnaliser la façon dont MessagePack met en forme vos données, `AddMessagePackProtocol` utilise un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="2448d-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2448d-273">Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2448d-274">Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2448d-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2448d-275">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.</span><span class="sxs-lookup"><span data-stu-id="2448d-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="2448d-276">Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="2448d-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="2448d-277">Par exemple, en affectant `MessagePackSecurity.Active` à la propriété statique la valeur `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="2448d-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="2448d-278">La définition `MessagePackSecurity.Active` de requiert l’installation manuelle [d’une version 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="2448d-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="2448d-279">`MessagePack`L’installation de la version 1.9. x met à niveau la version SignalR .</span><span class="sxs-lookup"><span data-stu-id="2448d-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="2448d-280">Lorsque `MessagePackSecurity.Active` n’a pas `MessagePackSecurity.UntrustedData` la valeur, un client malveillant peut provoquer un déni de service.</span><span class="sxs-lookup"><span data-stu-id="2448d-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="2448d-281">Définissez `MessagePackSecurity.Active` dans `Program.Main` , comme illustré dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2448d-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2448d-282">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="2448d-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2448d-283">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="2448d-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2448d-284">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="2448d-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="2448d-285">L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="2448d-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2448d-286">Client .NET</span><span class="sxs-lookup"><span data-stu-id="2448d-286">.NET client</span></span>

<span data-ttu-id="2448d-287">Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2448d-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2448d-288">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.</span><span class="sxs-lookup"><span data-stu-id="2448d-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2448d-289">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="2448d-289">JavaScript client</span></span>

<span data-ttu-id="2448d-290">La prise en charge de MessagePack pour le client JavaScript est fournie par le [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) package NPM.</span><span class="sxs-lookup"><span data-stu-id="2448d-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="2448d-291">Installez le package en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="2448d-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="2448d-292">Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="2448d-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="2448d-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="2448d-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="2448d-294">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="2448d-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2448d-295">Utilisez une `<script>` balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="2448d-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2448d-296">La bibliothèque se trouve sur *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2448d-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2448d-297">Lors de l’utilisation de l' `<script>` élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="2448d-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2448d-298">Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lors de la tentative de connexion à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2448d-299">*signalr.js* est également requis avant *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2448d-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2448d-300">L’ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` de à l' `HubConnectionBuilder` configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="2448d-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2448d-301">À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2448d-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="2448d-302">MessagePack excentriques</span><span class="sxs-lookup"><span data-stu-id="2448d-302">MessagePack quirks</span></span>

<span data-ttu-id="2448d-303">Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="2448d-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="2448d-304">MessagePack respecte la casse</span><span class="sxs-lookup"><span data-stu-id="2448d-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="2448d-305">Le protocole MessagePack respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="2448d-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="2448d-306">Par exemple, considérez la classe C# suivante :</span><span class="sxs-lookup"><span data-stu-id="2448d-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="2448d-307">Lors de l’envoi à partir du client JavaScript, vous devez utiliser des `PascalCased` noms de propriété, car la casse doit correspondre exactement à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="2448d-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="2448d-308">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="2448d-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="2448d-309">L’utilisation `camelCased` de noms n’est pas correctement liée à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="2448d-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="2448d-310">Vous pouvez contourner ce contournement en utilisant l' `Key` attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="2448d-311">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="2448d-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="2448d-312">DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation</span><span class="sxs-lookup"><span data-stu-id="2448d-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="2448d-313">Le protocole MessagePack ne fournit pas de méthode pour encoder la `Kind` valeur d’un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="2448d-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="2448d-314">Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub part du principe que la date d’entrée est au format UTC.</span><span class="sxs-lookup"><span data-stu-id="2448d-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="2448d-315">Si vous utilisez des `DateTime` valeurs en heure locale, nous vous recommandons de convertir au format UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="2448d-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="2448d-316">Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="2448d-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="2448d-317">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="2448d-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="2448d-318">DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="2448d-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="2448d-319">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas en charge le `timestamp96` type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2448d-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="2448d-320">Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur).</span><span class="sxs-lookup"><span data-stu-id="2448d-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="2448d-321">La valeur de `DateTime.MinValue` est `January 1, 0001` qui doit être encodée dans une `timestamp96` valeur.</span><span class="sxs-lookup"><span data-stu-id="2448d-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="2448d-322">Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="2448d-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="2448d-323">Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :</span><span class="sxs-lookup"><span data-stu-id="2448d-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="2448d-324">Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « manquante » ou `null` .</span><span class="sxs-lookup"><span data-stu-id="2448d-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="2448d-325">Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable ( `DateTime?` ) ou encodez une `bool` valeur distincte indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="2448d-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="2448d-326">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="2448d-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="2448d-327">Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="2448d-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="2448d-328">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="2448d-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="2448d-329">Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="2448d-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="2448d-330">Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur.</span><span class="sxs-lookup"><span data-stu-id="2448d-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="2448d-331">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="2448d-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="2448d-332">Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide du délégué de configuration passé à `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="2448d-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="2448d-333">Les vérifications de type sont plus strictes dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="2448d-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="2448d-334">Le protocole JSON Hub effectue des conversions de type pendant la désérialisation.</span><span class="sxs-lookup"><span data-stu-id="2448d-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="2448d-335">Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre ( `{ foo: 42 }` ) mais que la propriété de la classe .net est de type `string` , la valeur est convertie.</span><span class="sxs-lookup"><span data-stu-id="2448d-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="2448d-336">Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :</span><span class="sxs-lookup"><span data-stu-id="2448d-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="2448d-337">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="2448d-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="2448d-338">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="2448d-338">Related resources</span></span>

* [<span data-ttu-id="2448d-339">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="2448d-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2448d-340">Client .NET</span><span class="sxs-lookup"><span data-stu-id="2448d-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2448d-341">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="2448d-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
