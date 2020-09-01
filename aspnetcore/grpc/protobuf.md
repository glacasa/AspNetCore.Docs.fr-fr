---
title: Créer des messages Protobuf pour les applications .NET
author: jamesnk
description: Découvrez comment créer des messages Protobuf pour les applications .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/protobuf
ms.openlocfilehash: 60af1add9ae2f8b2b94bc19b65667d7af91fb122
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102664"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="68bad-103">Créer des messages Protobuf pour les applications .NET</span><span class="sxs-lookup"><span data-stu-id="68bad-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="68bad-104">Par [James Newton-King](https://twitter.com/jamesnk) et [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="68bad-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="68bad-105">gRPC utilise [Protobuf](https://developers.google.com/protocol-buffers) comme langage IDL (Interface Definition Language).</span><span class="sxs-lookup"><span data-stu-id="68bad-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="68bad-106">Protobuf IDL est un format indépendant de la langue permettant de spécifier les messages envoyés et reçus par les services gRPC.</span><span class="sxs-lookup"><span data-stu-id="68bad-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="68bad-107">Les messages Protobuf sont définis dans des `.proto` fichiers.</span><span class="sxs-lookup"><span data-stu-id="68bad-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="68bad-108">Ce document explique comment les concepts Protobuf sont mappés à .NET.</span><span class="sxs-lookup"><span data-stu-id="68bad-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="68bad-109">Messages Protobuf</span><span class="sxs-lookup"><span data-stu-id="68bad-109">Protobuf messages</span></span>

<span data-ttu-id="68bad-110">Les messages sont l’objet principal de transfert de données dans Protobuf.</span><span class="sxs-lookup"><span data-stu-id="68bad-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="68bad-111">Elles sont conceptuellement similaires aux classes .NET.</span><span class="sxs-lookup"><span data-stu-id="68bad-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="68bad-112">La définition du message précédent spécifie trois champs en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="68bad-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="68bad-113">Comme les propriétés sur les types .NET, chaque champ a un nom et un type.</span><span class="sxs-lookup"><span data-stu-id="68bad-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="68bad-114">Le type de champ peut être un [type valeur scalaire Protobuf](#scalar-value-types), par exemple `int32` , ou un autre message.</span><span class="sxs-lookup"><span data-stu-id="68bad-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="68bad-115">En plus d’un nom, chaque champ de la définition de message a un numéro unique.</span><span class="sxs-lookup"><span data-stu-id="68bad-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="68bad-116">Les numéros de champ sont utilisés pour identifier les champs lorsque le message est sérialisé vers Protobuf.</span><span class="sxs-lookup"><span data-stu-id="68bad-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="68bad-117">La sérialisation d’un petit nombre est plus rapide que la sérialisation de l’intégralité du nom de champ.</span><span class="sxs-lookup"><span data-stu-id="68bad-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="68bad-118">Comme les numéros de champ identifient un champ, il est important de prendre soin de les modifier.</span><span class="sxs-lookup"><span data-stu-id="68bad-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="68bad-119">Pour plus d’informations sur la modification des messages Protobuf <xref:grpc/versioning> , consultez.</span><span class="sxs-lookup"><span data-stu-id="68bad-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="68bad-120">Quand une application est générée, les outils Protobuf génèrent des types .NET à partir de `.proto` fichiers.</span><span class="sxs-lookup"><span data-stu-id="68bad-120">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="68bad-121">Le `Person` message génère une classe .net :</span><span class="sxs-lookup"><span data-stu-id="68bad-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="68bad-122">Pour plus d’informations sur les messages Protobuf, consultez le [Guide du langage Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="68bad-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="68bad-123">Types valeur scalaires</span><span class="sxs-lookup"><span data-stu-id="68bad-123">Scalar Value Types</span></span>

<span data-ttu-id="68bad-124">Protobuf prend en charge une plage de types valeur scalaire native.</span><span class="sxs-lookup"><span data-stu-id="68bad-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="68bad-125">Le tableau suivant les répertorie avec leur type C# équivalent :</span><span class="sxs-lookup"><span data-stu-id="68bad-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="68bad-126">Type Protobuf</span><span class="sxs-lookup"><span data-stu-id="68bad-126">Protobuf type</span></span> | <span data-ttu-id="68bad-127">Type C#</span><span class="sxs-lookup"><span data-stu-id="68bad-127">C# type</span></span>      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

### <a name="dates-and-times"></a><span data-ttu-id="68bad-128">Dates et heures</span><span class="sxs-lookup"><span data-stu-id="68bad-128">Dates and times</span></span>

<span data-ttu-id="68bad-129">Les types scalaires natifs ne fournissent pas de valeurs de date et d’heure équivalentes à. Les, <xref:System.DateTimeOffset> <xref:System.DateTime> et <xref:System.TimeSpan> .</span><span class="sxs-lookup"><span data-stu-id="68bad-129">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="68bad-130">Ces types peuvent être spécifiés à l’aide d’extensions de *types connus* de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="68bad-130">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="68bad-131">Ces extensions fournissent la génération de code et la prise en charge du runtime pour les types de champs complexes sur les plateformes prises en charge.</span><span class="sxs-lookup"><span data-stu-id="68bad-131">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="68bad-132">Le tableau suivant indique les types de date et d’heure :</span><span class="sxs-lookup"><span data-stu-id="68bad-132">The following table shows the date and time types:</span></span>

| <span data-ttu-id="68bad-133">Type .NET</span><span class="sxs-lookup"><span data-stu-id="68bad-133">.NET type</span></span>        | <span data-ttu-id="68bad-134">Protobuf, type connu</span><span class="sxs-lookup"><span data-stu-id="68bad-134">Protobuf Well-Known Type</span></span>    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

<span data-ttu-id="68bad-135">Les propriétés générées dans la classe C# ne sont pas les types de date et d’heure .NET.</span><span class="sxs-lookup"><span data-stu-id="68bad-135">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="68bad-136">Les propriétés utilisent les `Timestamp` `Duration` classes et dans l' `Google.Protobuf.WellKnownTypes` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="68bad-136">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="68bad-137">Ces classes fournissent des méthodes pour la conversion vers et à partir de `DateTimeOffset` , `DateTime` et `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="68bad-137">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> <span data-ttu-id="68bad-138">Le `Timestamp` type fonctionne avec des heures UTC.</span><span class="sxs-lookup"><span data-stu-id="68bad-138">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="68bad-139">`DateTimeOffset` les valeurs ont toujours un offset égal à zéro, et la `DateTime.Kind` propriété est toujours `DateTimeKind.Utc` .</span><span class="sxs-lookup"><span data-stu-id="68bad-139">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="68bad-140">Types Nullable</span><span class="sxs-lookup"><span data-stu-id="68bad-140">Nullable types</span></span>

<span data-ttu-id="68bad-141">La génération de code Protobuf pour C# utilise les types natifs, tels que `int` pour `int32` .</span><span class="sxs-lookup"><span data-stu-id="68bad-141">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="68bad-142">Par conséquent, les valeurs sont toujours incluses et ne peuvent pas être `null` .</span><span class="sxs-lookup"><span data-stu-id="68bad-142">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="68bad-143">Pour les valeurs qui requièrent explicite `null` , telles que l’utilisation `int?` de dans le code c#, les types connus de Protobuf incluent des wrappers qui sont compilés en types C# Nullable.</span><span class="sxs-lookup"><span data-stu-id="68bad-143">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="68bad-144">Pour les utiliser, importez `wrappers.proto` dans votre `.proto` fichier, comme le code suivant :</span><span class="sxs-lookup"><span data-stu-id="68bad-144">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="68bad-145">Protobuf utilise les types Nullable .NET, par exemple, `int?` pour la propriété de message générée.</span><span class="sxs-lookup"><span data-stu-id="68bad-145">Protobuf uses .NET nullable types, for example, `int?`, for the generated message property.</span></span>

<span data-ttu-id="68bad-146">Le tableau suivant présente la liste complète des types de wrappers avec leur type C# équivalent :</span><span class="sxs-lookup"><span data-stu-id="68bad-146">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="68bad-147">Type C#</span><span class="sxs-lookup"><span data-stu-id="68bad-147">C# type</span></span>   | <span data-ttu-id="68bad-148">Wrapper de type connu</span><span class="sxs-lookup"><span data-stu-id="68bad-148">Well-Known Type wrapper</span></span>       |
| --------- | ----------------------------- |
| `bool?`   | `google.protobuf.BoolValue`   |
| `double?` | `google.protobuf.DoubleValue` |
| `float?`  | `google.protobuf.FloatValue`  |
| `int?`    | `google.protobuf.Int32Value`  |
| `long?`   | `google.protobuf.Int64Value`  |
| `uint?`   | `google.protobuf.UInt32Value` |
| `ulong?`  | `google.protobuf.UInt64Value` |

### <a name="decimals"></a><span data-ttu-id="68bad-149">Décimales</span><span class="sxs-lookup"><span data-stu-id="68bad-149">Decimals</span></span>

<span data-ttu-id="68bad-150">Protobuf ne prend pas en charge en mode natif le `decimal` type .net, juste `double` et `float` .</span><span class="sxs-lookup"><span data-stu-id="68bad-150">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="68bad-151">Il existe une discussion continue dans le projet Protobuf sur la possibilité d’ajouter un type décimal standard aux types connus, avec la prise en charge de plateforme pour les langages et les infrastructures qui le prennent en charge.</span><span class="sxs-lookup"><span data-stu-id="68bad-151">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="68bad-152">Rien n’a encore été implémenté.</span><span class="sxs-lookup"><span data-stu-id="68bad-152">Nothing has been implemented yet.</span></span>

<span data-ttu-id="68bad-153">Il est possible de créer une définition de message pour représenter le `decimal` type qui fonctionne pour la sérialisation sécurisée entre les clients et les serveurs .net.</span><span class="sxs-lookup"><span data-stu-id="68bad-153">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="68bad-154">Toutefois, les développeurs sur d’autres plateformes doivent comprendre le format utilisé et implémenter leur propre gestion.</span><span class="sxs-lookup"><span data-stu-id="68bad-154">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="68bad-155">Création d’un type décimal personnalisé pour Protobuf</span><span class="sxs-lookup"><span data-stu-id="68bad-155">Creating a custom decimal type for Protobuf</span></span>

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

<span data-ttu-id="68bad-156">Le `nanos` champ représente les valeurs de `0.999_999_999` à `-0.999_999_999` .</span><span class="sxs-lookup"><span data-stu-id="68bad-156">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="68bad-157">Par exemple, la `decimal` valeur est `1.5m` représentée sous la forme `{ units = 1, nanos = 500_000_000 }` .</span><span class="sxs-lookup"><span data-stu-id="68bad-157">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="68bad-158">C’est la raison pour laquelle le `nanos` champ de cet exemple utilise le `sfixed32` type, qui s’encode plus efficacement que `int32` pour les valeurs plus élevées.</span><span class="sxs-lookup"><span data-stu-id="68bad-158">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="68bad-159">Si le `units` champ est négatif, le `nanos` champ doit également être négatif.</span><span class="sxs-lookup"><span data-stu-id="68bad-159">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="68bad-160">Il existe plusieurs autres algorithmes pour l’encodage de `decimal` valeurs en tant que chaînes d’octets, mais ce message est plus facile à comprendre que l’un d’entre eux.</span><span class="sxs-lookup"><span data-stu-id="68bad-160">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="68bad-161">Les valeurs ne sont pas affectées par Big-endian ou Little-endian sur différentes plateformes.</span><span class="sxs-lookup"><span data-stu-id="68bad-161">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="68bad-162">La conversion entre ce type et le `decimal` type BCL peut être implémentée en C# comme suit :</span><span class="sxs-lookup"><span data-stu-id="68bad-162">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a><span data-ttu-id="68bad-163">Collections</span><span class="sxs-lookup"><span data-stu-id="68bad-163">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="68bad-164">Listes</span><span class="sxs-lookup"><span data-stu-id="68bad-164">Lists</span></span>

<span data-ttu-id="68bad-165">Les listes dans Protobuf sont spécifiées à l’aide du `repeated` mot clé prefix sur un champ.</span><span class="sxs-lookup"><span data-stu-id="68bad-165">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="68bad-166">L’exemple suivant montre comment créer une liste :</span><span class="sxs-lookup"><span data-stu-id="68bad-166">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="68bad-167">Dans le code généré, `repeated` les champs sont représentés par le `Google.Protobuf.Collections.RepeatedField<T>` type générique.</span><span class="sxs-lookup"><span data-stu-id="68bad-167">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="68bad-168">L'objet `RepeatedField<T>` implémente l'objet <xref:System.Collections.Generic.IList%601>.</span><span class="sxs-lookup"><span data-stu-id="68bad-168">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="68bad-169">Vous pouvez donc utiliser des requêtes LINQ ou la convertir en un tableau ou une liste.</span><span class="sxs-lookup"><span data-stu-id="68bad-169">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="68bad-170">`RepeatedField<T>` les propriétés n’ont pas d’accesseur Set public.</span><span class="sxs-lookup"><span data-stu-id="68bad-170">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="68bad-171">Les éléments doivent être ajoutés à la collection existante.</span><span class="sxs-lookup"><span data-stu-id="68bad-171">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="68bad-172">Dictionnaires</span><span class="sxs-lookup"><span data-stu-id="68bad-172">Dictionaries</span></span>

<span data-ttu-id="68bad-173">Le <xref:System.Collections.Generic.IDictionary%602> type .net est représenté dans Protobuf à l’aide de `map<key_type, value_type>` .</span><span class="sxs-lookup"><span data-stu-id="68bad-173">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="68bad-174">Dans le code .NET généré, `map` les champs sont représentés par le `Google.Protobuf.Collections.MapField<TKey, TValue>` type générique.</span><span class="sxs-lookup"><span data-stu-id="68bad-174">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="68bad-175">L'objet `MapField<TKey, TValue>` implémente l'objet <xref:System.Collections.Generic.IDictionary%602>.</span><span class="sxs-lookup"><span data-stu-id="68bad-175">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="68bad-176">Comme les `repeated` Propriétés, les `map` Propriétés n’ont pas d’accesseur Set public.</span><span class="sxs-lookup"><span data-stu-id="68bad-176">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="68bad-177">Les éléments doivent être ajoutés à la collection existante.</span><span class="sxs-lookup"><span data-stu-id="68bad-177">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="68bad-178">Messages non structurés et conditionnels</span><span class="sxs-lookup"><span data-stu-id="68bad-178">Unstructured and conditional messages</span></span>

<span data-ttu-id="68bad-179">Protobuf est un format de messagerie contrat en premier.</span><span class="sxs-lookup"><span data-stu-id="68bad-179">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="68bad-180">Les messages d’une application, y compris ses champs et types, doivent être spécifiés dans `.proto` les fichiers lors de la génération de l’application.</span><span class="sxs-lookup"><span data-stu-id="68bad-180">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="68bad-181">La conception « contrat en premier » de Protobuf est très utile pour appliquer le contenu des messages, mais peut limiter les scénarios où un contrat strict n’est pas obligatoire :</span><span class="sxs-lookup"><span data-stu-id="68bad-181">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="68bad-182">Messages avec des charges utiles inconnues.</span><span class="sxs-lookup"><span data-stu-id="68bad-182">Messages with unknown payloads.</span></span> <span data-ttu-id="68bad-183">Par exemple, un message avec un champ qui peut contenir n’importe quel message.</span><span class="sxs-lookup"><span data-stu-id="68bad-183">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="68bad-184">Messages conditionnels.</span><span class="sxs-lookup"><span data-stu-id="68bad-184">Conditional messages.</span></span> <span data-ttu-id="68bad-185">Par exemple, un message retourné par un service gRPC peut être un résultat de réussite ou un résultat d’erreur.</span><span class="sxs-lookup"><span data-stu-id="68bad-185">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="68bad-186">Valeurs dynamiques.</span><span class="sxs-lookup"><span data-stu-id="68bad-186">Dynamic values.</span></span> <span data-ttu-id="68bad-187">Par exemple, un message avec un champ qui contient une collection non structurée de valeurs, similaire à JSON.</span><span class="sxs-lookup"><span data-stu-id="68bad-187">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="68bad-188">Protobuf offre des fonctionnalités et des types de langage pour prendre en charge ces scénarios.</span><span class="sxs-lookup"><span data-stu-id="68bad-188">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="68bad-189">Quelconque</span><span class="sxs-lookup"><span data-stu-id="68bad-189">Any</span></span>

<span data-ttu-id="68bad-190">Le `Any` type vous permet d’utiliser des messages en tant que types incorporés sans avoir leur `.proto` définition.</span><span class="sxs-lookup"><span data-stu-id="68bad-190">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="68bad-191">Pour utiliser le `Any` type, importez `any.proto` .</span><span class="sxs-lookup"><span data-stu-id="68bad-191">To use the `Any` type, import `any.proto`.</span></span>

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a><span data-ttu-id="68bad-192">Oneof</span><span class="sxs-lookup"><span data-stu-id="68bad-192">Oneof</span></span>

<span data-ttu-id="68bad-193">`oneof` les champs sont une fonctionnalité de langage.</span><span class="sxs-lookup"><span data-stu-id="68bad-193">`oneof` fields are a language feature.</span></span> <span data-ttu-id="68bad-194">Le compilateur gère le `oneof` mot clé lorsqu’il génère la classe de message.</span><span class="sxs-lookup"><span data-stu-id="68bad-194">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="68bad-195">L’utilisation `oneof` de pour spécifier un message de réponse qui peut retourner `Person` ou `Error` peut se présenter comme suit :</span><span class="sxs-lookup"><span data-stu-id="68bad-195">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

<span data-ttu-id="68bad-196">Les champs de l' `oneof` ensemble doivent avoir des numéros de champ uniques dans la déclaration globale du message.</span><span class="sxs-lookup"><span data-stu-id="68bad-196">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="68bad-197">Lors de l’utilisation de `oneof` , le code C# généré inclut une énumération qui spécifie les champs qui ont été définis.</span><span class="sxs-lookup"><span data-stu-id="68bad-197">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="68bad-198">Vous pouvez tester l’énumération pour rechercher le champ défini.</span><span class="sxs-lookup"><span data-stu-id="68bad-198">You can test the enum to find which field is set.</span></span> <span data-ttu-id="68bad-199">Les champs qui ne sont pas définis retournent `null` ou la valeur par défaut, plutôt que de lever une exception.</span><span class="sxs-lookup"><span data-stu-id="68bad-199">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a><span data-ttu-id="68bad-200">Value</span><span class="sxs-lookup"><span data-stu-id="68bad-200">Value</span></span>

<span data-ttu-id="68bad-201">Le `Value` type représente une valeur typée dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="68bad-201">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="68bad-202">Il peut s’agir d' `null` un nombre, d’une chaîne, d’une valeur booléenne, d’un dictionnaire de valeurs ( `Struct` ) ou d’une liste de valeurs ( `ValueList` ).</span><span class="sxs-lookup"><span data-stu-id="68bad-202">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="68bad-203">`Value` est un type Protobuf bien connu qui utilise la fonctionnalité décrite précédemment `oneof` .</span><span class="sxs-lookup"><span data-stu-id="68bad-203">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="68bad-204">Pour utiliser le `Value` type, importez `struct.proto` .</span><span class="sxs-lookup"><span data-stu-id="68bad-204">To use the `Value` type, import `struct.proto`.</span></span>

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

<span data-ttu-id="68bad-205">L’utilisation de `Value` direct peut être détaillée.</span><span class="sxs-lookup"><span data-stu-id="68bad-205">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="68bad-206">Une autre façon d’utiliser `Value` est la prise en charge intégrée de Protobuf pour le mappage des messages à JSON.</span><span class="sxs-lookup"><span data-stu-id="68bad-206">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="68bad-207">`JsonFormatter`Les types de Protobuf et `JsonWriter` peuvent être utilisés avec n’importe quel message Protobuf.</span><span class="sxs-lookup"><span data-stu-id="68bad-207">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="68bad-208">`Value` est particulièrement bien adapté à la conversion vers et à partir de JSON.</span><span class="sxs-lookup"><span data-stu-id="68bad-208">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="68bad-209">Il s’agit de l’équivalent JSON du code précédent :</span><span class="sxs-lookup"><span data-stu-id="68bad-209">This is the JSON equivalent of the previous code:</span></span>

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a><span data-ttu-id="68bad-210">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="68bad-210">Additional resources</span></span>

* [<span data-ttu-id="68bad-211">Guide du langage Protobuf</span><span class="sxs-lookup"><span data-stu-id="68bad-211">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
