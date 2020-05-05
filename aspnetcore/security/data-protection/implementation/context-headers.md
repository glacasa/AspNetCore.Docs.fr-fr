---
title: En-têtes de contexte dans ASP.NET Core
author: rick-anderson
description: Découvrez les détails de l’implémentation des en-têtes de contexte de protection des données ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 381cc137d1de87e87f36c3b32a6a551a318ed3cf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776953"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="c2254-103">En-têtes de contexte dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2254-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="c2254-104">Arrière-plan et théorie</span><span class="sxs-lookup"><span data-stu-id="c2254-104">Background and theory</span></span>

<span data-ttu-id="c2254-105">Dans le système de protection des données, une « clé » désigne un objet qui peut fournir des services de chiffrement authentifiés.</span><span class="sxs-lookup"><span data-stu-id="c2254-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="c2254-106">Chaque clé est identifiée par un ID unique (GUID) et elle contient des informations algorithmiques et des documents Entropic.</span><span class="sxs-lookup"><span data-stu-id="c2254-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="c2254-107">Il est prévu que chaque clé porte une entropie unique, mais le système ne peut pas le faire, et nous devons également tenir compte des développeurs qui peuvent changer manuellement l’anneau de clé en modifiant les informations algorithmiques d’une clé existante dans l’anneau de clé.</span><span class="sxs-lookup"><span data-stu-id="c2254-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="c2254-108">Pour répondre à nos exigences de sécurité, le système de protection des données présente un concept d' [agilité cryptographique](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), qui permet d’utiliser en toute sécurité une valeur Entropic unique sur plusieurs algorithmes de chiffrement.</span><span class="sxs-lookup"><span data-stu-id="c2254-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="c2254-109">La plupart des systèmes qui prennent en charge l’agilité de chiffrement le font en incluant des informations d’identification sur l’algorithme à l’intérieur de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="c2254-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="c2254-110">L’OID de l’algorithme est généralement un bon candidat pour cela.</span><span class="sxs-lookup"><span data-stu-id="c2254-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="c2254-111">Toutefois, l’un des problèmes que nous avons rencontré est qu’il existe plusieurs façons de spécifier le même algorithme : « AES » (CNG) et les classes AES, AesManaged, AesCryptoServiceProvider, AesCng et RijndaelManaged (données spécifiques) managées sont toutes identiques, et nous devons conserver un mappage de tous ces éléments sur l’OID correct.</span><span class="sxs-lookup"><span data-stu-id="c2254-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="c2254-112">Si un développeur souhaitait fournir un algorithme personnalisé (ou même une autre implémentation d’AES !), il aurait à nous dire son OID.</span><span class="sxs-lookup"><span data-stu-id="c2254-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="c2254-113">Cette étape d’enregistrement supplémentaire rend la configuration du système particulièrement pénible.</span><span class="sxs-lookup"><span data-stu-id="c2254-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="c2254-114">En arrière-plan, nous avons décidé que nous approchions le problème de la mauvaise direction.</span><span class="sxs-lookup"><span data-stu-id="c2254-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="c2254-115">Un OID vous indique l’algorithme, mais ce n’est pas vraiment une préoccupation.</span><span class="sxs-lookup"><span data-stu-id="c2254-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="c2254-116">Si nous avons besoin d’utiliser une seule valeur Entropic en toute sécurité dans deux algorithmes différents, il n’est pas nécessaire pour nous de savoir ce que sont les algorithmes.</span><span class="sxs-lookup"><span data-stu-id="c2254-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="c2254-117">Ce dont nous sommes particulièrement soucis, c’est la façon dont ils se comportent.</span><span class="sxs-lookup"><span data-stu-id="c2254-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="c2254-118">Tout autre algorithme de chiffrement par bloc symétrique est également une puissante permutation aléatoire : corriger les entrées (clé, mode chaînage, IV, texte brut) et la sortie de texte chiffré avec une probabilité écrasante est distincte de tout autre algorithme de chiffrement par bloc symétrique en raison des mêmes entrées.</span><span class="sxs-lookup"><span data-stu-id="c2254-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="c2254-119">De même, toute fonction de hachage à clé correcte est également une fonction de chiffrement aléatoire forte (PRF), et étant donné un jeu de données d’entrée fixe, sa sortie est très lourdement différente de toute autre fonction de hachage à clé.</span><span class="sxs-lookup"><span data-stu-id="c2254-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="c2254-120">Nous utilisons ce concept de Strong mot et PRFs pour créer un en-tête de contexte.</span><span class="sxs-lookup"><span data-stu-id="c2254-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="c2254-121">Cet en-tête de contexte agit essentiellement comme une empreinte numérique stable sur les algorithmes utilisés pour une opération donnée, et fournit l’agilité de chiffrement requise par le système de protection des données.</span><span class="sxs-lookup"><span data-stu-id="c2254-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="c2254-122">Cet en-tête est reproductible et est utilisé ultérieurement dans le cadre du [processus de dérivation de sous-clés](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="c2254-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="c2254-123">Il existe deux façons différentes de créer l’en-tête de contexte en fonction des modes de fonctionnement des algorithmes sous-jacents.</span><span class="sxs-lookup"><span data-stu-id="c2254-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="c2254-124">Chiffrement en mode CBC + authentification HMAC</span><span class="sxs-lookup"><span data-stu-id="c2254-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="c2254-125">L’en-tête de contexte est constitué des composants suivants :</span><span class="sxs-lookup"><span data-stu-id="c2254-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="c2254-126">[16 bits] La valeur 00 00, qui est un marqueur signifiant « chiffrement CBC + authentification HMAC ».</span><span class="sxs-lookup"><span data-stu-id="c2254-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="c2254-127">[32 bits] Longueur de la clé (en octets, Big-endian) de l’algorithme de chiffrement par bloc symétrique.</span><span class="sxs-lookup"><span data-stu-id="c2254-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="c2254-128">[32 bits] Taille de bloc (en octets, Big-endian) de l’algorithme de chiffrement par bloc symétrique.</span><span class="sxs-lookup"><span data-stu-id="c2254-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="c2254-129">[32 bits] Longueur de la clé (en octets, Big-endian) de l’algorithme HMAC.</span><span class="sxs-lookup"><span data-stu-id="c2254-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="c2254-130">(Actuellement, la taille de clé correspond toujours à la taille du condensé.)</span><span class="sxs-lookup"><span data-stu-id="c2254-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="c2254-131">[32 bits] Taille du condensé (en octets, Big-endian) de l’algorithme HMAC.</span><span class="sxs-lookup"><span data-stu-id="c2254-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="c2254-132">EncCBC (K_E, IV, ""), qui est la sortie de l’algorithme de chiffrement par bloc symétrique en fonction d’une entrée de chaîne vide et où IV est un vecteur tout zéro.</span><span class="sxs-lookup"><span data-stu-id="c2254-132">EncCBC(K_E, IV, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="c2254-133">La construction de K_E est décrite ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="c2254-133">The construction of K_E is described below.</span></span>

* <span data-ttu-id="c2254-134">MAC (K_H, ""), qui est la sortie de l’algorithme HMAC en fonction d’une entrée de chaîne vide.</span><span class="sxs-lookup"><span data-stu-id="c2254-134">MAC(K_H, ""), which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="c2254-135">La construction de K_H est décrite ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="c2254-135">The construction of K_H is described below.</span></span>

<span data-ttu-id="c2254-136">Idéalement, nous pourrions transmettre tous les vecteurs nuls pour K_E et K_H.</span><span class="sxs-lookup"><span data-stu-id="c2254-136">Ideally, we could pass all-zero vectors for K_E and K_H.</span></span> <span data-ttu-id="c2254-137">Toutefois, nous souhaitons éviter la situation dans laquelle l’algorithme sous-jacent vérifie l’existence de clés faibles avant d’effectuer des opérations (notamment DES et 3DES), ce qui empêche l’utilisation d’un modèle simple ou reproductible comme un vecteur tout zéro.</span><span class="sxs-lookup"><span data-stu-id="c2254-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="c2254-138">Au lieu de cela, nous utilisons le KDF NIST SP800-108 en mode compteur (voir [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) avec une clé, une étiquette et un contexte de longueur nulle, et HMACSHA512 comme le PRF sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="c2254-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="c2254-139">Nous dériverons | K_E | + | K_H | octets de sortie, puis décomposent le résultat en K_E et K_H eux-mêmes.</span><span class="sxs-lookup"><span data-stu-id="c2254-139">We derive | K_E | + | K_H | bytes of output, then decompose the result into K_E and K_H themselves.</span></span> <span data-ttu-id="c2254-140">Mathématiquement, ce qui est représenté comme suit.</span><span class="sxs-lookup"><span data-stu-id="c2254-140">Mathematically, this is represented as follows.</span></span>

<span data-ttu-id="c2254-141">(K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, Key = "", étiquette = "", context = "")</span><span class="sxs-lookup"><span data-stu-id="c2254-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="c2254-142">Exemple : AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="c2254-142">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="c2254-143">Par exemple, considérez le cas où l’algorithme de chiffrement par bloc symétrique est AES-192-CBC et que l’algorithme de validation est HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="c2254-143">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="c2254-144">Le système génère l’en-tête de contexte à l’aide des étapes suivantes.</span><span class="sxs-lookup"><span data-stu-id="c2254-144">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="c2254-145">Tout d’abord, Let (K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, Key = "", étiquette = "", context = ""), où | K_E | = 192 bits et | K_H | = 256 bits selon les algorithmes spécifiés.</span><span class="sxs-lookup"><span data-stu-id="c2254-145">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 256 bits per the specified algorithms.</span></span> <span data-ttu-id="c2254-146">Cela amène à K_E = 5BB6. 21DD et K_H = A04A.. 00A9 dans l’exemple ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="c2254-146">This leads to K_E = 5BB6..21DD and K_H = A04A..00A9 in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="c2254-147">Ensuite, calculez Enc_CBC (K_E, IV, "") pour AES-192-CBC donné IV = 0 \* et K_E comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="c2254-147">Next, compute Enc_CBC (K_E, IV, "") for AES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="c2254-148">résultat : = F474B1872B3B53E4721DE19C0841DB6F</span><span class="sxs-lookup"><span data-stu-id="c2254-148">result := F474B1872B3B53E4721DE19C0841DB6F</span></span>

<span data-ttu-id="c2254-149">Ensuite, calculez MAC (K_H, "") pour HMACSHA256 donné K_H comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="c2254-149">Next, compute MAC(K_H, "") for HMACSHA256 given K_H as above.</span></span>

<span data-ttu-id="c2254-150">résultat : = D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span><span class="sxs-lookup"><span data-stu-id="c2254-150">result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span></span>

<span data-ttu-id="c2254-151">Cela génère l’en-tête de contexte complet ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="c2254-151">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="c2254-152">Cet en-tête de contexte est l’empreinte numérique de la paire d’algorithmes de chiffrement authentifiée (AES-192-CBC Encryption + HMACSHA256 validation).</span><span class="sxs-lookup"><span data-stu-id="c2254-152">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="c2254-153">Les composants, comme décrit [ci-dessus](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) , sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="c2254-153">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="c2254-154">marqueur (00 00)</span><span class="sxs-lookup"><span data-stu-id="c2254-154">the marker (00 00)</span></span>

* <span data-ttu-id="c2254-155">longueur de la clé de chiffrement par bloc (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="c2254-155">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="c2254-156">taille du bloc de chiffrement par bloc (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="c2254-156">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="c2254-157">longueur de clé HMAC (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="c2254-157">the HMAC key length (00 00 00 20)</span></span>

* <span data-ttu-id="c2254-158">taille du condensé HMAC (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="c2254-158">the HMAC digest size (00 00 00 20)</span></span>

* <span data-ttu-id="c2254-159">sortie de chiffrement par bloc PRP (F4 74-DB 6F) et</span><span class="sxs-lookup"><span data-stu-id="c2254-159">the block cipher PRP output (F4 74 - DB 6F) and</span></span>

* <span data-ttu-id="c2254-160">résultat de la sortie HMAC PRF (D4 79-end).</span><span class="sxs-lookup"><span data-stu-id="c2254-160">the HMAC PRF output (D4 79 - end).</span></span>

> [!NOTE]
> <span data-ttu-id="c2254-161">L’en-tête du contexte d’authentification du chiffrement en mode CBC + HMAC est créé de la même façon que les implémentations des algorithmes soient fournies par le CNG Windows ou par les types SymmetricAlgorithm et KeyedHashAlgorithm managés.</span><span class="sxs-lookup"><span data-stu-id="c2254-161">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="c2254-162">Cela permet aux applications exécutées sur différents systèmes d’exploitation de produire de manière fiable le même en-tête de contexte même si les implémentations des algorithmes diffèrent entre les systèmes d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="c2254-162">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="c2254-163">(En pratique, KeyedHashAlgorithm n’a pas besoin d’être un HMAC approprié.</span><span class="sxs-lookup"><span data-stu-id="c2254-163">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="c2254-164">Il peut s’agir de n’importe quel type d’algorithme de hachage à clé.)</span><span class="sxs-lookup"><span data-stu-id="c2254-164">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="c2254-165">Exemple : 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="c2254-165">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="c2254-166">Tout d’abord, Let (K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, Key = "", étiquette = "", context = ""), où | K_E | = 192 bits et | K_H | = 160 bits selon les algorithmes spécifiés.</span><span class="sxs-lookup"><span data-stu-id="c2254-166">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 160 bits per the specified algorithms.</span></span> <span data-ttu-id="c2254-167">Cela amène à K_E = A219. E2BB et K_H = DC4A.. B464 dans l’exemple ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="c2254-167">This leads to K_E = A219..E2BB and K_H = DC4A..B464 in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="c2254-168">Ensuite, calculez Enc_CBC (K_E, IV, "") pour 3DES-192-CBC donné IV = 0 \* et K_E comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="c2254-168">Next, compute Enc_CBC (K_E, IV, "") for 3DES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="c2254-169">résultat : = ABB100F81E53E10E</span><span class="sxs-lookup"><span data-stu-id="c2254-169">result := ABB100F81E53E10E</span></span>

<span data-ttu-id="c2254-170">Ensuite, calculez MAC (K_H, "") pour HMACSHA1 donné K_H comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="c2254-170">Next, compute MAC(K_H, "") for HMACSHA1 given K_H as above.</span></span>

<span data-ttu-id="c2254-171">résultat : = 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span><span class="sxs-lookup"><span data-stu-id="c2254-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span></span>

<span data-ttu-id="c2254-172">Cela génère l’en-tête de contexte complet qui est une empreinte de la paire d’algorithmes de chiffrement authentifiés (3DES-192-CBC Encryption + HMACSHA1 validation), comme indiqué ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="c2254-172">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="c2254-173">Les composants s’interrompent comme suit :</span><span class="sxs-lookup"><span data-stu-id="c2254-173">The components break down as follows:</span></span>

* <span data-ttu-id="c2254-174">marqueur (00 00)</span><span class="sxs-lookup"><span data-stu-id="c2254-174">the marker (00 00)</span></span>

* <span data-ttu-id="c2254-175">longueur de la clé de chiffrement par bloc (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="c2254-175">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="c2254-176">taille du bloc de chiffrement par bloc (00 00 00 08)</span><span class="sxs-lookup"><span data-stu-id="c2254-176">the block cipher block size (00 00 00 08)</span></span>

* <span data-ttu-id="c2254-177">longueur de clé HMAC (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="c2254-177">the HMAC key length (00 00 00 14)</span></span>

* <span data-ttu-id="c2254-178">taille du condensé HMAC (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="c2254-178">the HMAC digest size (00 00 00 14)</span></span>

* <span data-ttu-id="c2254-179">sortie de chiffrement par bloc PRP (AB B1-E1 0E) et</span><span class="sxs-lookup"><span data-stu-id="c2254-179">the block cipher PRP output (AB B1 - E1 0E) and</span></span>

* <span data-ttu-id="c2254-180">sortie du PRF HMAC (76 EB-end).</span><span class="sxs-lookup"><span data-stu-id="c2254-180">the HMAC PRF output (76 EB - end).</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="c2254-181">Chiffrement + authentification du mode Galois/Counter</span><span class="sxs-lookup"><span data-stu-id="c2254-181">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="c2254-182">L’en-tête de contexte est constitué des composants suivants :</span><span class="sxs-lookup"><span data-stu-id="c2254-182">The context header consists of the following components:</span></span>

* <span data-ttu-id="c2254-183">[16 bits] La valeur 00 01, qui est un marqueur signifiant « GCM Encryption + Authentication ».</span><span class="sxs-lookup"><span data-stu-id="c2254-183">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="c2254-184">[32 bits] Longueur de la clé (en octets, Big-endian) de l’algorithme de chiffrement par bloc symétrique.</span><span class="sxs-lookup"><span data-stu-id="c2254-184">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="c2254-185">[32 bits] Taille de la valeur à usage unique (en octets, Big-endian) utilisée pendant les opérations de chiffrement authentifié.</span><span class="sxs-lookup"><span data-stu-id="c2254-185">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="c2254-186">(Pour notre système, cette valeur est fixe au format nonce = 96 bits.)</span><span class="sxs-lookup"><span data-stu-id="c2254-186">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="c2254-187">[32 bits] Taille de bloc (en octets, Big-endian) de l’algorithme de chiffrement par bloc symétrique.</span><span class="sxs-lookup"><span data-stu-id="c2254-187">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="c2254-188">(Pour GCM, cette valeur est fixe à la taille de bloc = 128 bits.)</span><span class="sxs-lookup"><span data-stu-id="c2254-188">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="c2254-189">[32 bits] Taille de la balise d’authentification (en octets, Big-endian) produite par la fonction de chiffrement authentifié.</span><span class="sxs-lookup"><span data-stu-id="c2254-189">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="c2254-190">(Pour notre système, ce problème est résolu au niveau de la balise Size = 128 bits.)</span><span class="sxs-lookup"><span data-stu-id="c2254-190">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="c2254-191">[128 bits] La balise de Enc_GCM (K_E, nonce, ""), qui est la sortie de l’algorithme de chiffrement par bloc symétrique en fonction d’une entrée de chaîne vide et où nonce est un vecteur tout zéro 96 bits.</span><span class="sxs-lookup"><span data-stu-id="c2254-191">[128 bits] The tag of Enc_GCM (K_E, nonce, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="c2254-192">K_E est dérivée à l’aide du même mécanisme que dans le scénario d’authentification CBC Encryption + HMAC.</span><span class="sxs-lookup"><span data-stu-id="c2254-192">K_E is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="c2254-193">Toutefois, étant donné qu’il n’y a pas de K_H en cours, nous avons essentiellement | K_H | = 0, et l’algorithme est réduit au formulaire ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="c2254-193">However, since there's no K_H in play here, we essentially have | K_H | = 0, and the algorithm collapses to the below form.</span></span>

<span data-ttu-id="c2254-194">K_E = SP800_108_CTR (PRF = HMACSHA512, Key = "", étiquette = "", context = "")</span><span class="sxs-lookup"><span data-stu-id="c2254-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-256-gcm"></a><span data-ttu-id="c2254-195">Exemple : AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="c2254-195">Example: AES-256-GCM</span></span>

<span data-ttu-id="c2254-196">Tout d’abord, laissez K_E = SP800_108_CTR (PRF = HMACSHA512, Key = "", étiquette = "", context = ""), où | K_E | = 256 bits.</span><span class="sxs-lookup"><span data-stu-id="c2254-196">First, let K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 256 bits.</span></span>

<span data-ttu-id="c2254-197">K_E : = 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span><span class="sxs-lookup"><span data-stu-id="c2254-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span></span>

<span data-ttu-id="c2254-198">Ensuite, calculez l’étiquette d’authentification de Enc_GCM (K_E, nonce, "") pour AES-256-GCM donnée nonce = 096 et K_E comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="c2254-198">Next, compute the authentication tag of Enc_GCM (K_E, nonce, "") for AES-256-GCM given nonce = 096 and K_E as above.</span></span>

<span data-ttu-id="c2254-199">résultat : = E7DCCE66DF855A323A6BB7BD7A59BE45</span><span class="sxs-lookup"><span data-stu-id="c2254-199">result := E7DCCE66DF855A323A6BB7BD7A59BE45</span></span>

<span data-ttu-id="c2254-200">Cela génère l’en-tête de contexte complet ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="c2254-200">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="c2254-201">Les composants s’interrompent comme suit :</span><span class="sxs-lookup"><span data-stu-id="c2254-201">The components break down as follows:</span></span>

* <span data-ttu-id="c2254-202">marqueur (00 01)</span><span class="sxs-lookup"><span data-stu-id="c2254-202">the marker (00 01)</span></span>

* <span data-ttu-id="c2254-203">longueur de la clé de chiffrement par bloc (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="c2254-203">the block cipher key length (00 00 00 20)</span></span>

* <span data-ttu-id="c2254-204">taille de la valeur à usage unique (00 00 00 0C)</span><span class="sxs-lookup"><span data-stu-id="c2254-204">the nonce size (00 00 00 0C)</span></span>

* <span data-ttu-id="c2254-205">taille du bloc de chiffrement par bloc (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="c2254-205">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="c2254-206">la taille de la balise d’authentification (00 00 00 10) et</span><span class="sxs-lookup"><span data-stu-id="c2254-206">the authentication tag size (00 00 00 10) and</span></span>

* <span data-ttu-id="c2254-207">balise d’authentification à partir de l’exécution du chiffrement par blocs (E7 DC-end).</span><span class="sxs-lookup"><span data-stu-id="c2254-207">the authentication tag from running the block cipher (E7 DC - end).</span></span>
