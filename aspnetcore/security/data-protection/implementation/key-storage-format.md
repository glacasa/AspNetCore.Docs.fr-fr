---
title: Format de stockage clé dans ASP.NET Core
author: rick-anderson
description: Apprenez les détails de l’implémentation du format de stockage clé ASP.NET Core Data Protection.
ms.author: riande
ms.date: 04/08/2020
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 3072c673791b589027a910b80eaba52052eb9311
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976935"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="ffebc-103">Format de stockage clé dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ffebc-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="ffebc-104">Les objets sont stockés au repos dans la représentation XML.</span><span class="sxs-lookup"><span data-stu-id="ffebc-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="ffebc-105">L’annuaire par défaut pour le stockage des clés est :</span><span class="sxs-lookup"><span data-stu-id="ffebc-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="ffebc-106">Windows: '%LOCALAPPDATA%'ASP.NET-DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="ffebc-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="ffebc-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span><span class="sxs-lookup"><span data-stu-id="ffebc-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="ffebc-108">L’élément \<clé></span><span class="sxs-lookup"><span data-stu-id="ffebc-108">The \<key> element</span></span>

<span data-ttu-id="ffebc-109">Les clés existent en tant qu’objets de haut niveau dans le référentiel clé.</span><span class="sxs-lookup"><span data-stu-id="ffebc-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="ffebc-110">Par les clés de convention ont le nom de fichier **clé-guid.xml**, où «guid» est l’id de la clé.</span><span class="sxs-lookup"><span data-stu-id="ffebc-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="ffebc-111">Chaque fichier contient une seule clé.</span><span class="sxs-lookup"><span data-stu-id="ffebc-111">Each such file contains a single key.</span></span> <span data-ttu-id="ffebc-112">Le format du fichier est le suivant.</span><span class="sxs-lookup"><span data-stu-id="ffebc-112">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="ffebc-113">L’élément \<clé> contient les attributs suivants et les éléments pour enfants :</span><span class="sxs-lookup"><span data-stu-id="ffebc-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="ffebc-114">L’id clé. Cette valeur est traitée comme faisant autorité; le nom de fichier est tout simplement une gentillesse pour la lisibilité humaine.</span><span class="sxs-lookup"><span data-stu-id="ffebc-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="ffebc-115">La version \<de l’élément clé>, actuellement fixé à 1.</span><span class="sxs-lookup"><span data-stu-id="ffebc-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="ffebc-116">La création, l’activation et les dates d’expiration de la clé.</span><span class="sxs-lookup"><span data-stu-id="ffebc-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="ffebc-117">Un \<descripteur> élément, qui contient des informations sur la mise en œuvre de chiffrement authentifiée contenue dans cette clé.</span><span class="sxs-lookup"><span data-stu-id="ffebc-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="ffebc-118">Dans l’exemple ci-dessus, l’id de la clé est '80732141-ec8f-4b80-af9c-c4d1ff8901', il a été créé et activé le 19 mars 2015, et il a une durée de vie de 90 jours.</span><span class="sxs-lookup"><span data-stu-id="ffebc-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="ffebc-119">(Occasionnellement, la date d’activation peut être légèrement avant la date de création comme dans cet exemple.</span><span class="sxs-lookup"><span data-stu-id="ffebc-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="ffebc-120">Cela est dû à un nit dans la façon dont les API fonctionnent et est inoffensif dans la pratique.)</span><span class="sxs-lookup"><span data-stu-id="ffebc-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="ffebc-121">Le \<descripteur> élément</span><span class="sxs-lookup"><span data-stu-id="ffebc-121">The \<descriptor> element</span></span>

<span data-ttu-id="ffebc-122">Le \<descripteur externe> élément contient un attribut deserializerType, qui est le nom d’assemblage qualifié d’un type qui implémente IAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="ffebc-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="ffebc-123">Ce type est responsable \<de la lecture du descripteur intérieur> élément et de l’analyse de l’information contenue dans.</span><span class="sxs-lookup"><span data-stu-id="ffebc-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="ffebc-124">Le format particulier \<du descripteur> élément dépend de l’implémentation du crypteur authentifié encapsulé par la clé, et chaque type de déséialisateur s’attend à un format légèrement différent pour cela.</span><span class="sxs-lookup"><span data-stu-id="ffebc-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="ffebc-125">En général, cependant, cet élément contiendra des informations algorithmiques (noms, types, OIDs, ou similaires) et du matériel clé secret.</span><span class="sxs-lookup"><span data-stu-id="ffebc-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="ffebc-126">Dans l’exemple ci-dessus, le descripteur précise que cette clé enveloppe le chiffrement AES-256-CBC - validation HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="ffebc-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="ffebc-127">L’élément \<> cryptésecret</span><span class="sxs-lookup"><span data-stu-id="ffebc-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="ffebc-128">Un \*\* &lt;élément&gt; cryptésecret\*\* qui contient la forme cryptée du matériel clé secret peut être présent si [le cryptage des secrets au repos est activé](xref:security/data-protection/implementation/key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="ffebc-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="ffebc-129">L’attribut `decryptorType` est le nom d’assemblage qualifié d’un type qui implémente [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="ffebc-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="ffebc-130">Ce type est responsable de la lecture de l’élément \*\* &lt;interne cryptéKey&gt; \*\* et de le décrypter pour récupérer le texte clair d’origine.</span><span class="sxs-lookup"><span data-stu-id="ffebc-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="ffebc-131">Comme `<descriptor>`avec , le `<encryptedSecret>` format particulier de l’élément dépend du mécanisme de cryptage au repos dans l’utilisation.</span><span class="sxs-lookup"><span data-stu-id="ffebc-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="ffebc-132">Dans l’exemple ci-dessus, la clé principale est cryptée à l’aide de Windows DPAPI par commentaire.</span><span class="sxs-lookup"><span data-stu-id="ffebc-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="ffebc-133">La \<révocation> élément</span><span class="sxs-lookup"><span data-stu-id="ffebc-133">The \<revocation> element</span></span>

<span data-ttu-id="ffebc-134">Les révocations existent en tant qu’objets de haut niveau dans le référentiel clé.</span><span class="sxs-lookup"><span data-stu-id="ffebc-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="ffebc-135">Par les révocations de convention ont le nom de fichier **revocation-'timestamp.xml** (pour révoquer toutes les clés avant une date spécifique) ou **révocation-guid.xml** (pour révoquer une clé spécifique).</span><span class="sxs-lookup"><span data-stu-id="ffebc-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="ffebc-136">Chaque fichier contient \<un seul élément de révocation>.</span><span class="sxs-lookup"><span data-stu-id="ffebc-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="ffebc-137">Pour les révocations de clés individuelles, le contenu du fichier sera comme ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="ffebc-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="ffebc-138">Dans ce cas, seule la clé spécifiée est révoquée.</span><span class="sxs-lookup"><span data-stu-id="ffebc-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="ffebc-139">Toutefois, si l’id clé est « EN », comme dans l’exemple ci-dessous, toutes les clés dont la date de création est antérieure à la date de révocation spécifiée sont révoquées.</span><span class="sxs-lookup"><span data-stu-id="ffebc-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="ffebc-140">La \<raison> élément n’est jamais lue par le système.</span><span class="sxs-lookup"><span data-stu-id="ffebc-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="ffebc-141">C’est tout simplement un endroit pratique pour stocker une raison lisible à l’homme pour la révocation.</span><span class="sxs-lookup"><span data-stu-id="ffebc-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
