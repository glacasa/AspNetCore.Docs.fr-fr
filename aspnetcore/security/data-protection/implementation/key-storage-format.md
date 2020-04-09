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
# <a name="key-storage-format-in-aspnet-core"></a>Format de stockage clé dans ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Les objets sont stockés au repos dans la représentation XML. L’annuaire par défaut pour le stockage des clés est :

* Windows: '%LOCALAPPDATA%'ASP.NET-DataProtection-Keys\*
* macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*

## <a name="the-key-element"></a>L’élément \<clé>

Les clés existent en tant qu’objets de haut niveau dans le référentiel clé. Par les clés de convention ont le nom de fichier **clé-guid.xml**, où «guid» est l’id de la clé. Chaque fichier contient une seule clé. Le format du fichier est le suivant.

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

L’élément \<clé> contient les attributs suivants et les éléments pour enfants :

* L’id clé. Cette valeur est traitée comme faisant autorité; le nom de fichier est tout simplement une gentillesse pour la lisibilité humaine.

* La version \<de l’élément clé>, actuellement fixé à 1.

* La création, l’activation et les dates d’expiration de la clé.

* Un \<descripteur> élément, qui contient des informations sur la mise en œuvre de chiffrement authentifiée contenue dans cette clé.

Dans l’exemple ci-dessus, l’id de la clé est '80732141-ec8f-4b80-af9c-c4d1ff8901', il a été créé et activé le 19 mars 2015, et il a une durée de vie de 90 jours. (Occasionnellement, la date d’activation peut être légèrement avant la date de création comme dans cet exemple. Cela est dû à un nit dans la façon dont les API fonctionnent et est inoffensif dans la pratique.)

## <a name="the-descriptor-element"></a>Le \<descripteur> élément

Le \<descripteur externe> élément contient un attribut deserializerType, qui est le nom d’assemblage qualifié d’un type qui implémente IAuthenticatedEncryptorDescriptorDeserializer. Ce type est responsable \<de la lecture du descripteur intérieur> élément et de l’analyse de l’information contenue dans.

Le format particulier \<du descripteur> élément dépend de l’implémentation du crypteur authentifié encapsulé par la clé, et chaque type de déséialisateur s’attend à un format légèrement différent pour cela. En général, cependant, cet élément contiendra des informations algorithmiques (noms, types, OIDs, ou similaires) et du matériel clé secret. Dans l’exemple ci-dessus, le descripteur précise que cette clé enveloppe le chiffrement AES-256-CBC - validation HMACSHA256.

## <a name="the-encryptedsecret-element"></a>L’élément \<> cryptésecret

Un ** &lt;élément&gt; cryptésecret** qui contient la forme cryptée du matériel clé secret peut être présent si [le cryptage des secrets au repos est activé](xref:security/data-protection/implementation/key-encryption-at-rest). L’attribut `decryptorType` est le nom d’assemblage qualifié d’un type qui implémente [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Ce type est responsable de la lecture de l’élément ** &lt;interne cryptéKey&gt; ** et de le décrypter pour récupérer le texte clair d’origine.

Comme `<descriptor>`avec , le `<encryptedSecret>` format particulier de l’élément dépend du mécanisme de cryptage au repos dans l’utilisation. Dans l’exemple ci-dessus, la clé principale est cryptée à l’aide de Windows DPAPI par commentaire.

## <a name="the-revocation-element"></a>La \<révocation> élément

Les révocations existent en tant qu’objets de haut niveau dans le référentiel clé. Par les révocations de convention ont le nom de fichier **revocation-'timestamp.xml** (pour révoquer toutes les clés avant une date spécifique) ou **révocation-guid.xml** (pour révoquer une clé spécifique). Chaque fichier contient \<un seul élément de révocation>.

Pour les révocations de clés individuelles, le contenu du fichier sera comme ci-dessous.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

Dans ce cas, seule la clé spécifiée est révoquée. Toutefois, si l’id clé est « EN », comme dans l’exemple ci-dessous, toutes les clés dont la date de création est antérieure à la date de révocation spécifiée sont révoquées.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

La \<raison> élément n’est jamais lue par le système. C’est tout simplement un endroit pratique pour stocker une raison lisible à l’homme pour la révocation.
