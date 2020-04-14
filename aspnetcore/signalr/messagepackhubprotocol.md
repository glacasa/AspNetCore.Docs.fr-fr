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
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>Utilisez le protocole SignalR MessagePack Hub pour ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

Cet article suppose que le lecteur est familier avec les sujets abordés dans [Get Started](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Qu’est-ce que MessagePack ?

[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact. Il est utile lorsque la performance et la bande passante sont une préoccupation, car il crée des messages plus petits par rapport à [JSON](https://www.json.org/). Les messages binaires sont illisibles lorsque vous regardez les traces et les journaux réseau à moins que les octets ne soient passés par un analyseur MessagePack. SignalRa intégré le support intégré pour le format MessagePack et fournit des API pour le client et le serveur à utiliser.

## <a name="configure-messagepack-on-the-server"></a>Configurer MessagePack sur le serveur

Pour activer le protocole MessagePack Hub `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` sur le serveur, installez le paquet dans votre application. Dans `Startup.ConfigureServices` la méthode, `AddMessagePackProtocol` `AddSignalR` ajouter à l’appel pour activer le support MessagePack sur le serveur.

> [!NOTE]
> JSON est activé par défaut. L’ajout de MessagePack permet de prendre en charge les clients JSON et MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Pour personnaliser la façon dont MessagePack formatera vos données, `AddMessagePackProtocol` prend un délégué pour configurer les options. Dans ce délégué, la `SerializerOptions` propriété peut être utilisée pour configurer les options de sérialisation MessagePack. Pour plus d’informations sur le fonctionnement des résolveurs, visitez la bibliothèque MessagePack à [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir comment ils doivent être manipulés.

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
> Nous recommandons fortement d’examiner [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés. Par exemple, `.WithSecurity(MessagePackSecurity.UntrustedData)` appeler lors `SerializerOptions`du remplacement de la .

## <a name="configure-messagepack-on-the-client"></a>Configurer MessagePack sur le client

> [!NOTE]
> JSON est activé par défaut pour les clients pris en charge. Les clients ne peuvent prendre en charge qu’un seul protocole. L’ajout du support MessagePack remplacera tous les protocoles précédemment configurés.

### <a name="net-client"></a>Client .NET

Pour activer MessagePack dans le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` client .NET, installez le paquet et appelez `AddMessagePackProtocol` . `HubConnectionBuilder`

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Cet `AddMessagePackProtocol` appel prend un délégué pour configurer des options tout comme le serveur.

### <a name="javascript-client"></a>Client JavaScript

MessagePack support pour le client JavaScript est fourni par le [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) paquet npm. Installer le paquet en exécutant la commande suivante dans une coque de commande :

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Après l’installation du paquet npm, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en faisant référence au fichier suivant :

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée. Utilisez `<script>` une balise pour créer une référence. La bibliothèque peut être trouvée à *node_modules’msgpack5'dist’msgpack5.js*.

> [!NOTE]
> Lors de `<script>` l’utilisation de l’élément, l’ordre est important. Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lorsque vous essayez de se connecter à MessagePack. *signalr.js* est également nécessaire avant *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` à `HubConnectionBuilder` la configuration du client pour utiliser le protocole MessagePack lors de la connexion à un serveur.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Pour le moment, il n’existe pas d’options de configuration pour le protocole MessagePack sur le client JavaScript.

## <a name="messagepack-quirks"></a>Les bizarreries messagePack

Il y a quelques problèmes à connaître lors de l’utilisation du protocole Hub MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack est sensible aux cas

Le protocole MessagePack est sensible aux cas. Par exemple, considérez la classe C suivante :

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Lors de l’envoi du client `PascalCased` JavaScript, vous devez utiliser les noms de propriété, puisque le boîtier doit correspondre à la classe C exactement. Par exemple :

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

L’utilisation de `camelCased` noms ne se lie pas correctement à la classe C. Vous pouvez contourner ce `Key` sujet en utilisant l’attribut pour spécifier un nom différent pour la propriété MessagePack. Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind n’est pas conservé lors de la sérialisation/deserialisation

Le protocole MessagePack ne fournit pas un `Kind` moyen `DateTime`d’encoder la valeur d’un . Par conséquent, lors de la désétération d’une date, le protocole `DateTime.Kind` `DateTimeKind.Local` MessagePack Hub se convertira au format UTC si l’est autrement, il ne touchera pas l’heure et la passera telle quel. Si vous travaillez `DateTime` avec des valeurs, nous vous recommandons de vous convertir à UTC avant de les envoyer. Convertissez-les de l’UTC à l’heure locale lorsque vous les recevez.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue n’est pas pris en charge par MessagePack dans JavaScript

La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilisée par le client `timestamp96` JavaScript ne prend pas en charge le type dans MessagePack. Ce type est utilisé pour coder des valeurs de date très grandes (soit très tôt dans le passé ou très loin à l’avenir). La valeur `DateTime.MinValue` `January 1, 0001`de est , qui doit `timestamp96` être codé dans une valeur. Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge. Lorsque `DateTime.MinValue` le client JavaScript est reçu, l’erreur suivante est lancée :

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Habituellement, `DateTime.MinValue` est utilisé pour coder `null` un "manquant" ou la valeur. Si vous devez coder cette valeur dans MessagePack, utilisez une `DateTime` valeur nulle (`DateTime?`) ou codez une valeur distincte `bool` indiquant si la date est présente.

Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>MessagePack support dans l’environnement de compilation « à l’avance »

La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation. En conséquence, il n’est pas pris en charge par défaut sur les environnements qui utilisent "à l’avance" compilation (comme Xamarin iOS ou Unity). Il est possible d’utiliser MessagePack dans ces environnements en « pré-générant » le code sérialisateur/déséialiseur. Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Une fois que vous avez pré-généré les sérialisateurs, vous pouvez les enregistrer à l’aide du délégué de configuration passé à `AddMessagePackProtocol`:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Les contrôles de type sont plus stricts dans MessagePack

Le protocole JSON Hub effectuera des conversions de type pendant la désétération. Par exemple, si l’objet entrant a une`{ foo: 42 }`valeur de propriété qui est un `string`nombre ( ) mais la propriété sur la classe .NET est de type, la valeur sera convertie. Cependant, MessagePack n’effectue pas cette conversion et lancera une exception qui peut être vu dans les journaux côté serveur (et dans la console):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Ressources associées

* [Bien démarrer](xref:tutorials/signalr)
* [.NET client](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Cet article suppose que le lecteur est familier avec les sujets abordés dans [Get Started](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Qu’est-ce que MessagePack ?

[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact. Il est utile lorsque la performance et la bande passante sont une préoccupation, car il crée des messages plus petits par rapport à [JSON](https://www.json.org/). Les messages binaires sont illisibles lorsque vous regardez les traces et les journaux réseau à moins que les octets ne soient passés par un analyseur MessagePack. SignalRa intégré le support intégré pour le format MessagePack, et fournit des API pour le client et le serveur à utiliser.

## <a name="configure-messagepack-on-the-server"></a>Configurer MessagePack sur le serveur

Pour activer le protocole MessagePack Hub `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` sur le serveur, installez le paquet dans votre application. Dans `Startup.ConfigureServices` la méthode, `AddMessagePackProtocol` `AddSignalR` ajouter à l’appel pour activer le support MessagePack sur le serveur.

> [!NOTE]
> JSON est activé par défaut. L’ajout de MessagePack permet de prendre en charge les clients JSON et MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Pour personnaliser la façon dont MessagePack formatera vos données, `AddMessagePackProtocol` prend un délégué pour configurer les options. Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack. Pour plus d’informations sur le fonctionnement des résolveurs, visitez la bibliothèque MessagePack à [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir comment ils doivent être manipulés.

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
> Nous recommandons fortement d’examiner [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés. Par exemple, `MessagePackSecurity.Active` définir la `MessagePackSecurity.UntrustedData`propriété statique à . Réglage `MessagePackSecurity.Active` des besoins en installation manuelle d’une [version 1.9.x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). Installation `MessagePack` 1.9.x mises à SignalR niveau de la version utilise. Quand `MessagePackSecurity.Active` n’est `MessagePackSecurity.UntrustedData`pas configuré à , un client malveillant peut causer un déni de service. Définissez, `MessagePackSecurity.Active` `Program.Main`comme indiqué dans le code suivant :

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurer MessagePack sur le client

> [!NOTE]
> JSON est activé par défaut pour les clients pris en charge. Les clients ne peuvent prendre en charge qu’un seul protocole. L’ajout du support MessagePack remplacera tous les protocoles précédemment configurés.

### <a name="net-client"></a>Client .NET

Pour activer MessagePack dans le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` client .NET, installez le paquet et appelez `AddMessagePackProtocol` . `HubConnectionBuilder`

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Cet `AddMessagePackProtocol` appel prend un délégué pour configurer des options tout comme le serveur.

### <a name="javascript-client"></a>Client JavaScript

MessagePack support pour le client JavaScript est fourni par le [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) paquet npm. Installer le paquet en exécutant la commande suivante dans une coque de commande :

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Après l’installation du paquet npm, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en faisant référence au fichier suivant :

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée. Utilisez `<script>` une balise pour créer une référence. La bibliothèque peut être trouvée à *node_modules’msgpack5'dist’msgpack5.js*.

> [!NOTE]
> Lors de `<script>` l’utilisation de l’élément, l’ordre est important. Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lorsque vous essayez de se connecter à MessagePack. *signalr.js* est également nécessaire avant *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` à `HubConnectionBuilder` la configuration du client pour utiliser le protocole MessagePack lors de la connexion à un serveur.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Pour le moment, il n’existe pas d’options de configuration pour le protocole MessagePack sur le client JavaScript.

## <a name="messagepack-quirks"></a>Les bizarreries messagePack

Il y a quelques problèmes à connaître lors de l’utilisation du protocole Hub MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack est sensible aux cas

Le protocole MessagePack est sensible aux cas. Par exemple, considérez la classe C suivante :

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Lors de l’envoi du client `PascalCased` JavaScript, vous devez utiliser les noms de propriété, puisque le boîtier doit correspondre à la classe C exactement. Par exemple :

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

L’utilisation de `camelCased` noms ne se lie pas correctement à la classe C. Vous pouvez contourner ce `Key` sujet en utilisant l’attribut pour spécifier un nom différent pour la propriété MessagePack. Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind n’est pas conservé lors de la sérialisation/deserialisation

Le protocole MessagePack ne fournit pas un `Kind` moyen `DateTime`d’encoder la valeur d’un . Par conséquent, lors de la désétération d’une date, le protocole MessagePack Hub suppose que la date d’entrée est en format UTC. Si vous travaillez `DateTime` avec des valeurs à l’heure locale, nous vous recommandons de vous convertir à UTC avant de les envoyer. Convertissez-les de l’UTC à l’heure locale lorsque vous les recevez.

Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue n’est pas pris en charge par MessagePack dans JavaScript

La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilisée par le client `timestamp96` JavaScript ne prend pas en charge le type dans MessagePack. Ce type est utilisé pour coder des valeurs de date très grandes (soit très tôt dans le passé ou très loin à l’avenir). La valeur `DateTime.MinValue` `January 1, 0001`de est , qui doit `timestamp96` être codé dans une valeur. Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge. Lorsque `DateTime.MinValue` le client JavaScript est reçu, l’erreur suivante est lancée :

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Habituellement, `DateTime.MinValue` est utilisé pour coder `null` un "manquant" ou la valeur. Si vous devez coder cette valeur dans MessagePack, utilisez une `DateTime` valeur nulle (`DateTime?`) ou codez une valeur distincte `bool` indiquant si la date est présente.

Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>MessagePack support dans l’environnement de compilation « à l’avance »

La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation. En conséquence, il n’est pas pris en charge par défaut sur les environnements qui utilisent "à l’avance" compilation (comme Xamarin iOS ou Unity). Il est possible d’utiliser MessagePack dans ces environnements en « pré-générant » le code sérialisateur/déséialiseur. Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Une fois que vous avez pré-généré les sérialisateurs, vous pouvez les enregistrer à l’aide du délégué de configuration passé à `AddMessagePackProtocol`:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Les contrôles de type sont plus stricts dans MessagePack

Le protocole JSON Hub effectuera des conversions de type pendant la désétération. Par exemple, si l’objet entrant a une`{ foo: 42 }`valeur de propriété qui est un `string`nombre ( ) mais la propriété sur la classe .NET est de type, la valeur sera convertie. Cependant, MessagePack n’effectue pas cette conversion et lancera une exception qui peut être vu dans les journaux côté serveur (et dans la console):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Ressources associées

* [Bien démarrer](xref:tutorials/signalr)
* [.NET client](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Cet article suppose que le lecteur est familier avec les sujets abordés dans [Get Started](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Qu’est-ce que MessagePack ?

[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact. Il est utile lorsque la performance et la bande passante sont une préoccupation, car il crée des messages plus petits par rapport à [JSON](https://www.json.org/). Les messages binaires sont illisibles lorsque vous regardez les traces et les journaux réseau à moins que les octets ne soient passés par un analyseur MessagePack. SignalRa intégré le support intégré pour le format MessagePack, et fournit des API pour le client et le serveur à utiliser.

## <a name="configure-messagepack-on-the-server"></a>Configurer MessagePack sur le serveur

Pour activer le protocole MessagePack Hub `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` sur le serveur, installez le paquet dans votre application. Dans `Startup.ConfigureServices` la méthode, `AddMessagePackProtocol` `AddSignalR` ajouter à l’appel pour activer le support MessagePack sur le serveur.

> [!NOTE]
> JSON est activé par défaut. L’ajout de MessagePack permet de prendre en charge les clients JSON et MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Pour personnaliser la façon dont MessagePack formatera vos données, `AddMessagePackProtocol` prend un délégué pour configurer les options. Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack. Pour plus d’informations sur le fonctionnement des résolveurs, visitez la bibliothèque MessagePack à [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir comment ils doivent être manipulés.

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
> Nous recommandons fortement d’examiner [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés. Par exemple, `MessagePackSecurity.Active` définir la `MessagePackSecurity.UntrustedData`propriété statique à . Réglage `MessagePackSecurity.Active` des besoins en installation manuelle d’une [version 1.9.x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). Installation `MessagePack` 1.9.x mises à SignalR niveau de la version utilise. Quand `MessagePackSecurity.Active` n’est `MessagePackSecurity.UntrustedData`pas configuré à , un client malveillant peut causer un déni de service. Définissez, `MessagePackSecurity.Active` `Program.Main`comme indiqué dans le code suivant :

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurer MessagePack sur le client

> [!NOTE]
> JSON est activé par défaut pour les clients pris en charge. Les clients ne peuvent prendre en charge qu’un seul protocole. L’ajout du support MessagePack remplacera tous les protocoles précédemment configurés.

### <a name="net-client"></a>Client .NET

Pour activer MessagePack dans le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` client .NET, installez le paquet et appelez `AddMessagePackProtocol` . `HubConnectionBuilder`

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Cet `AddMessagePackProtocol` appel prend un délégué pour configurer des options tout comme le serveur.

### <a name="javascript-client"></a>Client JavaScript

MessagePack support pour le client JavaScript est fourni par le [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) paquet npm. Installer le paquet en exécutant la commande suivante dans une coque de commande :

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Après l’installation du paquet npm, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en faisant référence au fichier suivant :

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée. Utilisez `<script>` une balise pour créer une référence. La bibliothèque peut être trouvée à *node_modules’msgpack5'dist’msgpack5.js*.

> [!NOTE]
> Lors de `<script>` l’utilisation de l’élément, l’ordre est important. Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lorsque vous essayez de se connecter à MessagePack. *signalr.js* est également nécessaire avant *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` à `HubConnectionBuilder` la configuration du client pour utiliser le protocole MessagePack lors de la connexion à un serveur.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Pour le moment, il n’existe pas d’options de configuration pour le protocole MessagePack sur le client JavaScript.

## <a name="messagepack-quirks"></a>Les bizarreries messagePack

Il y a quelques problèmes à connaître lors de l’utilisation du protocole Hub MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack est sensible aux cas

Le protocole MessagePack est sensible aux cas. Par exemple, considérez la classe C suivante :

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Lors de l’envoi du client `PascalCased` JavaScript, vous devez utiliser les noms de propriété, puisque le boîtier doit correspondre à la classe C exactement. Par exemple :

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

L’utilisation de `camelCased` noms ne se lie pas correctement à la classe C. Vous pouvez contourner ce `Key` sujet en utilisant l’attribut pour spécifier un nom différent pour la propriété MessagePack. Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind n’est pas conservé lors de la sérialisation/deserialisation

Le protocole MessagePack ne fournit pas un `Kind` moyen `DateTime`d’encoder la valeur d’un . Par conséquent, lors de la désétération d’une date, le protocole MessagePack Hub suppose que la date d’entrée est en format UTC. Si vous travaillez `DateTime` avec des valeurs à l’heure locale, nous vous recommandons de vous convertir à UTC avant de les envoyer. Convertissez-les de l’UTC à l’heure locale lorsque vous les recevez.

Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue n’est pas pris en charge par MessagePack dans JavaScript

La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilisée par le client `timestamp96` JavaScript ne prend pas en charge le type dans MessagePack. Ce type est utilisé pour coder des valeurs de date très grandes (soit très tôt dans le passé ou très loin à l’avenir). La valeur `DateTime.MinValue` `January 1, 0001` de qui doit être `timestamp96` codée dans une valeur. Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge. Lorsque `DateTime.MinValue` le client JavaScript est reçu, l’erreur suivante est lancée :

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Habituellement, `DateTime.MinValue` est utilisé pour coder `null` un "manquant" ou la valeur. Si vous devez coder cette valeur dans MessagePack, utilisez une `DateTime` valeur nulle (`DateTime?`) ou codez une valeur distincte `bool` indiquant si la date est présente.

Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>MessagePack support dans l’environnement de compilation « à l’avance »

La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation. En conséquence, il n’est pas pris en charge par défaut sur les environnements qui utilisent "à l’avance" compilation (comme Xamarin iOS ou Unity). Il est possible d’utiliser MessagePack dans ces environnements en « pré-générant » le code sérialisateur/déséialiseur. Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Une fois que vous avez pré-généré les sérialisateurs, vous pouvez les enregistrer à l’aide du délégué de configuration passé à `AddMessagePackProtocol`:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Les contrôles de type sont plus stricts dans MessagePack

Le protocole JSON Hub effectuera des conversions de type pendant la désétération. Par exemple, si l’objet entrant a une`{ foo: 42 }`valeur de propriété qui est un `string`nombre ( ) mais la propriété sur la classe .NET est de type, la valeur sera convertie. Cependant, MessagePack n’effectue pas cette conversion et lancera une exception qui peut être vu dans les journaux côté serveur (et dans la console):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Pour plus d’informations sur cette limitation, voir GitHub question [aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Ressources associées

* [Bien démarrer](xref:tutorials/signalr)
* [.NET client](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)

::: moniker-end
