---
title: Hébergement d’ASP.NET Core image dans le conteneur à l’aide de dockr compose avec HTTPs
author: ravipal
description: Découvrez comment héberger des images ASP.NET Core avec Docker Compose via HTTPs
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
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
uid: security/docker-compose-https
ms.openlocfilehash: 75a205c1eb21394ed36c00359f0dc4ca7e6d09e0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631639"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="94edc-103">Hébergement d’images ASP.NET Core avec Docker Compose sur HTTPs</span><span class="sxs-lookup"><span data-stu-id="94edc-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="94edc-104">ASP.NET Core utilise le [protocole HTTPS par défaut](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="94edc-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="94edc-105">[Https](https://en.wikipedia.org/wiki/HTTPS) s’appuie sur des [certificats](https://en.wikipedia.org/wiki/Public_key_certificate) pour l’approbation, l’identité et le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="94edc-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="94edc-106">Ce document explique comment exécuter des images conteneur prégénérées avec HTTPs.</span><span class="sxs-lookup"><span data-stu-id="94edc-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="94edc-107">Consultez [développement d’Applications ASP.net core avec l’arrimeur sur https](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="94edc-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="94edc-108">Cet exemple requiert l' [ancrage 17,06](https://docs.docker.com/release-notes/docker-ce) ou une version ultérieure du [client dockr](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="94edc-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="94edc-109">Prérequis</span><span class="sxs-lookup"><span data-stu-id="94edc-109">Prerequisites</span></span>

<span data-ttu-id="94edc-110">Le [Kit de développement logiciel (SDK) .net Core 2,2](https://dotnet.microsoft.com/download) ou version ultérieure est requis pour certaines des instructions contenues dans ce document.</span><span class="sxs-lookup"><span data-stu-id="94edc-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="94edc-111">Certificats</span><span class="sxs-lookup"><span data-stu-id="94edc-111">Certificates</span></span>

<span data-ttu-id="94edc-112">Un certificat d’une [autorité de certification](https://wikipedia.org/wiki/Certificate_authority) est requis pour l' [Hébergement de production](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) pour un domaine.</span><span class="sxs-lookup"><span data-stu-id="94edc-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="94edc-113">[Let's Encrypt](https://letsencrypt.org/) est une autorité de certification qui offre des certificats gratuits.</span><span class="sxs-lookup"><span data-stu-id="94edc-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="94edc-114">Ce document utilise des [certificats de développement auto-signés](https://wikipedia.org/wiki/Self-signed_certificate) pour héberger des images prégénérées sur `localhost` .</span><span class="sxs-lookup"><span data-stu-id="94edc-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="94edc-115">Les instructions sont similaires à l’utilisation de certificats de production.</span><span class="sxs-lookup"><span data-stu-id="94edc-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="94edc-116">Pour les certificats de production :</span><span class="sxs-lookup"><span data-stu-id="94edc-116">For production certificates:</span></span>

* <span data-ttu-id="94edc-117">L' `dotnet dev-certs` outil n’est pas requis.</span><span class="sxs-lookup"><span data-stu-id="94edc-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="94edc-118">Les certificats n’ont pas besoin d’être stockés à l’emplacement utilisé dans les instructions.</span><span class="sxs-lookup"><span data-stu-id="94edc-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="94edc-119">Stockez les certificats dans n’importe quel emplacement en dehors de l’annuaire de sites.</span><span class="sxs-lookup"><span data-stu-id="94edc-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="94edc-120">Les instructions contenues dans la section suivante contiennent des certificats de montage de volume dans des conteneurs à l’aide `volumes` de la propriété dans *docker-compose. yml.*</span><span class="sxs-lookup"><span data-stu-id="94edc-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="94edc-121">Vous pouvez ajouter des certificats dans des images de conteneur à l’aide d’une `COPY` commande dans un *fichier dockerfile*, mais cela n’est pas recommandé.</span><span class="sxs-lookup"><span data-stu-id="94edc-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="94edc-122">La copie de certificats dans une image n’est pas recommandée pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="94edc-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="94edc-123">Il est donc difficile d’utiliser la même image pour le test avec des certificats de développeur.</span><span class="sxs-lookup"><span data-stu-id="94edc-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="94edc-124">Il est donc difficile d’utiliser la même image pour l’hébergement avec des certificats de production.</span><span class="sxs-lookup"><span data-stu-id="94edc-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="94edc-125">Il y a un risque significatif de divulgation de certificats.</span><span class="sxs-lookup"><span data-stu-id="94edc-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="94edc-126">Démarrage d’un conteneur avec prise en charge de HTTPS à l’aide de dockr compose</span><span class="sxs-lookup"><span data-stu-id="94edc-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="94edc-127">Utilisez les instructions suivantes pour la configuration de votre système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="94edc-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="94edc-128">Windows utilisant des conteneurs Linux</span><span class="sxs-lookup"><span data-stu-id="94edc-128">Windows using Linux containers</span></span>

<span data-ttu-id="94edc-129">Générer un certificat et configurer l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="94edc-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="94edc-130">Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="94edc-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="94edc-131">Créez un fichier _docker-compose. Debug. yml_ avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="94edc-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="94edc-132">Le mot de passe spécifié dans le fichier compose de l’ancrage doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="94edc-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="94edc-133">Démarrez le conteneur avec ASP.NET Core configuré pour le protocole HTTPs :</span><span class="sxs-lookup"><span data-stu-id="94edc-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="94edc-134">macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="94edc-134">macOS or Linux</span></span>

<span data-ttu-id="94edc-135">Générer un certificat et configurer l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="94edc-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="94edc-136">`dotnet dev-certs https --trust` est pris en charge uniquement sur macOS et Windows.</span><span class="sxs-lookup"><span data-stu-id="94edc-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="94edc-137">Vous devez faire confiance aux certificats sur Linux de la manière prise en charge par votre distribution.</span><span class="sxs-lookup"><span data-stu-id="94edc-137">You need to trust certificates on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="94edc-138">Il est probable que vous ayez besoin d’approuver le certificat dans votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="94edc-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="94edc-139">Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="94edc-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="94edc-140">Créez un fichier _docker-compose. Debug. yml_ avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="94edc-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="94edc-141">Le mot de passe spécifié dans le fichier compose de l’ancrage doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="94edc-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="94edc-142">Démarrez le conteneur avec ASP.NET Core configuré pour le protocole HTTPs :</span><span class="sxs-lookup"><span data-stu-id="94edc-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="94edc-143">Windows utilisant des conteneurs Windows</span><span class="sxs-lookup"><span data-stu-id="94edc-143">Windows using Windows containers</span></span>

<span data-ttu-id="94edc-144">Générer un certificat et configurer l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="94edc-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="94edc-145">Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="94edc-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="94edc-146">Créez un fichier _docker-compose. Debug. yml_ avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="94edc-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="94edc-147">Le mot de passe spécifié dans le fichier compose de l’ancrage doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="94edc-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="94edc-148">Démarrez le conteneur avec ASP.NET Core configuré pour le protocole HTTPs :</span><span class="sxs-lookup"><span data-stu-id="94edc-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
