---
title: Utiliser le protocole MessagePack Hub SignalR dans pour ASP.net Core
author: bradygaster
description: Ajoutez le protocole MessagePack Hub à SignalRASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 8db7598d978848f13bf5b21a873340b38154e9a8
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777200"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="4a6ea-103">Utiliser le protocole MessagePack Hub SignalR dans pour ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="4a6ea-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4a6ea-104">Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="4a6ea-105">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="4a6ea-105">What is MessagePack?</span></span>

<span data-ttu-id="4a6ea-106">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="4a6ea-107">Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="4a6ea-108">Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="4a6ea-109">dispose d’une prise en charge intégrée du format MessagePack et fournit des API pour le client et le serveur à utiliser.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="4a6ea-110">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="4a6ea-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="4a6ea-111">Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="4a6ea-112">Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="4a6ea-113">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-113">JSON is enabled by default.</span></span> <span data-ttu-id="4a6ea-114">L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="4a6ea-115">Pour personnaliser la façon dont MessagePack met en forme `AddMessagePackProtocol` vos données, utilise un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="4a6ea-116">Dans ce délégué, la `SerializerOptions` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="4a6ea-117">Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="4a6ea-118">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="4a6ea-119">Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="4a6ea-120">Par exemple, appeler `.WithSecurity(MessagePackSecurity.UntrustedData)` lors du remplacement `SerializerOptions`de.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="4a6ea-121">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="4a6ea-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="4a6ea-122">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="4a6ea-123">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="4a6ea-124">L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="4a6ea-125">Client .NET</span><span class="sxs-lookup"><span data-stu-id="4a6ea-125">.NET client</span></span>

<span data-ttu-id="4a6ea-126">Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="4a6ea-127">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="4a6ea-128">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a6ea-128">JavaScript client</span></span>

<span data-ttu-id="4a6ea-129">La prise en charge de MessagePack pour le client JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) est fournie par le package NPM.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="4a6ea-130">Installez le package en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="4a6ea-131">Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="4a6ea-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="4a6ea-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="4a6ea-133">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="4a6ea-134">Utilisez une `<script>` balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="4a6ea-135">La bibliothèque se trouve sur *node_modules \msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="4a6ea-136">Lors de l' `<script>` utilisation de l’élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="4a6ea-137">Si *signalr-Protocol-msgpack. js* est référencé avant *msgpack5. js*, une erreur se produit lors de la tentative de connexion à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="4a6ea-138">*signalr. js* est également requis avant *signalr-Protocol-msgpack. js*.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="4a6ea-139">L' `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ajout de `HubConnectionBuilder` à l’configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="4a6ea-140">À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="4a6ea-141">MessagePack excentriques</span><span class="sxs-lookup"><span data-stu-id="4a6ea-141">MessagePack quirks</span></span>

<span data-ttu-id="4a6ea-142">Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="4a6ea-143">MessagePack respecte la casse</span><span class="sxs-lookup"><span data-stu-id="4a6ea-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="4a6ea-144">Le protocole MessagePack respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="4a6ea-145">Par exemple, considérez la classe C# suivante :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="4a6ea-146">Lors de l’envoi à partir du client JavaScript, `PascalCased` vous devez utiliser des noms de propriété, car la casse doit correspondre exactement à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="4a6ea-147">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="4a6ea-148">L' `camelCased` utilisation de noms n’est pas correctement liée à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="4a6ea-149">Vous pouvez contourner ce contournement en utilisant `Key` l’attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="4a6ea-150">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="4a6ea-151">DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation</span><span class="sxs-lookup"><span data-stu-id="4a6ea-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="4a6ea-152">Le protocole MessagePack ne fournit pas de méthode pour encoder `Kind` la `DateTime`valeur d’un.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="4a6ea-153">Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub est converti au format UTC si le `DateTime.Kind` n' `DateTimeKind.Local` est pas le cas, et il ne peut pas toucher l’heure et le passer tel quel.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="4a6ea-154">Si vous utilisez des `DateTime` valeurs, nous vous recommandons de convertir au format UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="4a6ea-155">Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="4a6ea-156">DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a6ea-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="4a6ea-157">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas `timestamp96` en charge le type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="4a6ea-158">Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="4a6ea-159">La valeur de `DateTime.MinValue` est `January 1, 0001`, qui doit être encodée dans une `timestamp96` valeur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="4a6ea-160">Pour cette raison, l' `DateTime.MinValue` envoi à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="4a6ea-161">Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="4a6ea-162">Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « `null` manquante » ou.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="4a6ea-163">Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable`DateTime?`() ou encodez une valeur distincte `bool` indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="4a6ea-164">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNETSignalR/#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="4a6ea-165">Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="4a6ea-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="4a6ea-166">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="4a6ea-167">Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="4a6ea-168">Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="4a6ea-169">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="4a6ea-170">Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide `AddMessagePackProtocol`du délégué de configuration passé à :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="4a6ea-171">Les vérifications de type sont plus strictes dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="4a6ea-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="4a6ea-172">Le protocole JSON Hub effectue des conversions de type pendant la désérialisation.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="4a6ea-173">Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre`{ foo: 42 }`() mais que la propriété de la classe .net est `string`de type, la valeur est convertie.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="4a6ea-174">Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="4a6ea-175">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNETSignalR/#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="4a6ea-176">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="4a6ea-176">Related resources</span></span>

* [<span data-ttu-id="4a6ea-177">Démarrer</span><span class="sxs-lookup"><span data-stu-id="4a6ea-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="4a6ea-178">Client .NET</span><span class="sxs-lookup"><span data-stu-id="4a6ea-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="4a6ea-179">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a6ea-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="4a6ea-180">Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="4a6ea-181">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="4a6ea-181">What is MessagePack?</span></span>

<span data-ttu-id="4a6ea-182">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="4a6ea-183">Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="4a6ea-184">Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="4a6ea-185">dispose d’une prise en charge intégrée du format MessagePack et fournit des API que le client et le serveur doivent utiliser.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="4a6ea-186">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="4a6ea-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="4a6ea-187">Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="4a6ea-188">Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="4a6ea-189">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-189">JSON is enabled by default.</span></span> <span data-ttu-id="4a6ea-190">L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="4a6ea-191">Pour personnaliser la façon dont MessagePack met en forme `AddMessagePackProtocol` vos données, utilise un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="4a6ea-192">Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="4a6ea-193">Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="4a6ea-194">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="4a6ea-195">Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="4a6ea-196">Par exemple, en affectant à `MessagePackSecurity.Active` `MessagePackSecurity.UntrustedData`la propriété statique la valeur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="4a6ea-197">La `MessagePackSecurity.Active` définition de requiert l’installation manuelle [d’une version 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="4a6ea-198">L' `MessagePack` installation de la version 1.9. x met SignalR à niveau la version.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="4a6ea-199">Lorsque `MessagePackSecurity.Active` n’a pas la `MessagePackSecurity.UntrustedData`valeur, un client malveillant peut provoquer un déni de service.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="4a6ea-200">Définissez `MessagePackSecurity.Active` dans `Program.Main`, comme illustré dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="4a6ea-201">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="4a6ea-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="4a6ea-202">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="4a6ea-203">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="4a6ea-204">L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="4a6ea-205">Client .NET</span><span class="sxs-lookup"><span data-stu-id="4a6ea-205">.NET client</span></span>

<span data-ttu-id="4a6ea-206">Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="4a6ea-207">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="4a6ea-208">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a6ea-208">JavaScript client</span></span>

<span data-ttu-id="4a6ea-209">La prise en charge de MessagePack pour le client JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) est fournie par le package NPM.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="4a6ea-210">Installez le package en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="4a6ea-211">Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="4a6ea-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="4a6ea-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="4a6ea-213">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="4a6ea-214">Utilisez une `<script>` balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="4a6ea-215">La bibliothèque se trouve sur *node_modules \msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="4a6ea-216">Lors de l' `<script>` utilisation de l’élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="4a6ea-217">Si *signalr-Protocol-msgpack. js* est référencé avant *msgpack5. js*, une erreur se produit lors de la tentative de connexion à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="4a6ea-218">*signalr. js* est également requis avant *signalr-Protocol-msgpack. js*.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="4a6ea-219">L' `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ajout de `HubConnectionBuilder` à l’configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="4a6ea-220">À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="4a6ea-221">MessagePack excentriques</span><span class="sxs-lookup"><span data-stu-id="4a6ea-221">MessagePack quirks</span></span>

<span data-ttu-id="4a6ea-222">Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="4a6ea-223">MessagePack respecte la casse</span><span class="sxs-lookup"><span data-stu-id="4a6ea-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="4a6ea-224">Le protocole MessagePack respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="4a6ea-225">Par exemple, considérez la classe C# suivante :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="4a6ea-226">Lors de l’envoi à partir du client JavaScript, `PascalCased` vous devez utiliser des noms de propriété, car la casse doit correspondre exactement à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="4a6ea-227">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="4a6ea-228">L' `camelCased` utilisation de noms n’est pas correctement liée à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="4a6ea-229">Vous pouvez contourner ce contournement en utilisant `Key` l’attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="4a6ea-230">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="4a6ea-231">DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation</span><span class="sxs-lookup"><span data-stu-id="4a6ea-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="4a6ea-232">Le protocole MessagePack ne fournit pas de méthode pour encoder `Kind` la `DateTime`valeur d’un.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="4a6ea-233">Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub part du principe que la date d’entrée est au format UTC.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="4a6ea-234">Si vous utilisez des `DateTime` valeurs en heure locale, nous vous recommandons de convertir au format UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="4a6ea-235">Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="4a6ea-236">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNETSignalR/#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="4a6ea-237">DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a6ea-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="4a6ea-238">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas `timestamp96` en charge le type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="4a6ea-239">Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="4a6ea-240">La valeur de `DateTime.MinValue` est `January 1, 0001`, qui doit être encodée dans une `timestamp96` valeur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="4a6ea-241">Pour cette raison, l' `DateTime.MinValue` envoi à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="4a6ea-242">Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="4a6ea-243">Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « `null` manquante » ou.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="4a6ea-244">Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable`DateTime?`() ou encodez une valeur distincte `bool` indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="4a6ea-245">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNETSignalR/#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="4a6ea-246">Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="4a6ea-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="4a6ea-247">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="4a6ea-248">Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="4a6ea-249">Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="4a6ea-250">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="4a6ea-251">Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide `AddMessagePackProtocol`du délégué de configuration passé à :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="4a6ea-252">Les vérifications de type sont plus strictes dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="4a6ea-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="4a6ea-253">Le protocole JSON Hub effectue des conversions de type pendant la désérialisation.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="4a6ea-254">Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre`{ foo: 42 }`() mais que la propriété de la classe .net est `string`de type, la valeur est convertie.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="4a6ea-255">Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="4a6ea-256">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNETSignalR/#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="4a6ea-257">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="4a6ea-257">Related resources</span></span>

* [<span data-ttu-id="4a6ea-258">Démarrer</span><span class="sxs-lookup"><span data-stu-id="4a6ea-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="4a6ea-259">Client .NET</span><span class="sxs-lookup"><span data-stu-id="4a6ea-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="4a6ea-260">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a6ea-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4a6ea-261">Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="4a6ea-262">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="4a6ea-262">What is MessagePack?</span></span>

<span data-ttu-id="4a6ea-263">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="4a6ea-264">Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="4a6ea-265">Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="4a6ea-266">dispose d’une prise en charge intégrée du format MessagePack et fournit des API que le client et le serveur doivent utiliser.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="4a6ea-267">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="4a6ea-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="4a6ea-268">Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="4a6ea-269">Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="4a6ea-270">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-270">JSON is enabled by default.</span></span> <span data-ttu-id="4a6ea-271">L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="4a6ea-272">Pour personnaliser la façon dont MessagePack met en forme `AddMessagePackProtocol` vos données, utilise un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="4a6ea-273">Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="4a6ea-274">Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="4a6ea-275">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="4a6ea-276">Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="4a6ea-277">Par exemple, en affectant à `MessagePackSecurity.Active` `MessagePackSecurity.UntrustedData`la propriété statique la valeur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="4a6ea-278">La `MessagePackSecurity.Active` définition de requiert l’installation manuelle [d’une version 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="4a6ea-279">L' `MessagePack` installation de la version 1.9. x met SignalR à niveau la version.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="4a6ea-280">Lorsque `MessagePackSecurity.Active` n’a pas la `MessagePackSecurity.UntrustedData`valeur, un client malveillant peut provoquer un déni de service.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="4a6ea-281">Définissez `MessagePackSecurity.Active` dans `Program.Main`, comme illustré dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="4a6ea-282">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="4a6ea-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="4a6ea-283">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="4a6ea-284">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="4a6ea-285">L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="4a6ea-286">Client .NET</span><span class="sxs-lookup"><span data-stu-id="4a6ea-286">.NET client</span></span>

<span data-ttu-id="4a6ea-287">Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="4a6ea-288">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="4a6ea-289">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a6ea-289">JavaScript client</span></span>

<span data-ttu-id="4a6ea-290">La prise en charge de MessagePack pour le client JavaScript [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) est fournie par le package NPM.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="4a6ea-291">Installez le package en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="4a6ea-292">Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="4a6ea-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="4a6ea-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="4a6ea-294">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="4a6ea-295">Utilisez une `<script>` balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="4a6ea-296">La bibliothèque se trouve sur *node_modules \msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="4a6ea-297">Lors de l' `<script>` utilisation de l’élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="4a6ea-298">Si *signalr-Protocol-msgpack. js* est référencé avant *msgpack5. js*, une erreur se produit lors de la tentative de connexion à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="4a6ea-299">*signalr. js* est également requis avant *signalr-Protocol-msgpack. js*.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="4a6ea-300">L' `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ajout de `HubConnectionBuilder` à l’configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="4a6ea-301">À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="4a6ea-302">MessagePack excentriques</span><span class="sxs-lookup"><span data-stu-id="4a6ea-302">MessagePack quirks</span></span>

<span data-ttu-id="4a6ea-303">Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="4a6ea-304">MessagePack respecte la casse</span><span class="sxs-lookup"><span data-stu-id="4a6ea-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="4a6ea-305">Le protocole MessagePack respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="4a6ea-306">Par exemple, considérez la classe C# suivante :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="4a6ea-307">Lors de l’envoi à partir du client JavaScript, `PascalCased` vous devez utiliser des noms de propriété, car la casse doit correspondre exactement à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="4a6ea-308">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="4a6ea-309">L' `camelCased` utilisation de noms n’est pas correctement liée à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="4a6ea-310">Vous pouvez contourner ce contournement en utilisant `Key` l’attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="4a6ea-311">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="4a6ea-312">DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation</span><span class="sxs-lookup"><span data-stu-id="4a6ea-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="4a6ea-313">Le protocole MessagePack ne fournit pas de méthode pour encoder `Kind` la `DateTime`valeur d’un.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="4a6ea-314">Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub part du principe que la date d’entrée est au format UTC.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="4a6ea-315">Si vous utilisez des `DateTime` valeurs en heure locale, nous vous recommandons de convertir au format UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="4a6ea-316">Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="4a6ea-317">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNETSignalR/#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="4a6ea-318">DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a6ea-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="4a6ea-319">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas `timestamp96` en charge le type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="4a6ea-320">Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="4a6ea-321">La valeur de `DateTime.MinValue` est `January 1, 0001` qui doit être encodée dans une `timestamp96` valeur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="4a6ea-322">Pour cette raison, l' `DateTime.MinValue` envoi à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="4a6ea-323">Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="4a6ea-324">Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « `null` manquante » ou.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="4a6ea-325">Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable`DateTime?`() ou encodez une valeur distincte `bool` indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="4a6ea-326">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNETSignalR/#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="4a6ea-327">Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="4a6ea-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="4a6ea-328">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="4a6ea-329">Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="4a6ea-330">Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="4a6ea-331">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="4a6ea-332">Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide `AddMessagePackProtocol`du délégué de configuration passé à :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="4a6ea-333">Les vérifications de type sont plus strictes dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="4a6ea-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="4a6ea-334">Le protocole JSON Hub effectue des conversions de type pendant la désérialisation.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="4a6ea-335">Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre`{ foo: 42 }`() mais que la propriété de la classe .net est `string`de type, la valeur est convertie.</span><span class="sxs-lookup"><span data-stu-id="4a6ea-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="4a6ea-336">Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :</span><span class="sxs-lookup"><span data-stu-id="4a6ea-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="4a6ea-337">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNETSignalR/#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="4a6ea-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="4a6ea-338">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="4a6ea-338">Related resources</span></span>

* [<span data-ttu-id="4a6ea-339">Démarrer</span><span class="sxs-lookup"><span data-stu-id="4a6ea-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="4a6ea-340">Client .NET</span><span class="sxs-lookup"><span data-stu-id="4a6ea-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="4a6ea-341">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a6ea-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
