---
title: Hébergement ASP.NET image de base dans le conteneur utilisant le docker composer avec HTTPS
author: ravipal
description: Apprenez à accueillir ASP.NET Images de base avec Docker Compose sur HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Let's Encrypt
uid: security/docker-compose-https
ms.openlocfilehash: 616ccf906e98534ffda08c0c2b6d0a171f063cc1
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381823"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="fae3e-103">Hébergement ASP.NET images de base avec Docker Compose sur HTTPS</span><span class="sxs-lookup"><span data-stu-id="fae3e-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="fae3e-104">ASP.NET Core utilise [HTTPS par défaut](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="fae3e-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="fae3e-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) s’appuie sur [des certificats](https://en.wikipedia.org/wiki/Public_key_certificate) de confiance, d’identité et de cryptage.</span><span class="sxs-lookup"><span data-stu-id="fae3e-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="fae3e-106">Ce document explique comment exécuter des images de conteneurs pré-construites avec HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fae3e-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="fae3e-107">Voir [Développer ASP.NET applications de base avec Docker sur HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="fae3e-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="fae3e-108">Cet échantillon nécessite [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) ou plus tard du [client Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="fae3e-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fae3e-109">Prérequis</span><span class="sxs-lookup"><span data-stu-id="fae3e-109">Prerequisites</span></span>

<span data-ttu-id="fae3e-110">Le [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) ou plus tard est nécessaire pour certaines des instructions contenues dans ce document.</span><span class="sxs-lookup"><span data-stu-id="fae3e-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="fae3e-111">Certificats</span><span class="sxs-lookup"><span data-stu-id="fae3e-111">Certificates</span></span>

<span data-ttu-id="fae3e-112">Un certificat d’une autorité de [certificat](https://wikipedia.org/wiki/Certificate_authority) est requis pour [l’hébergement de production](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) pour un domaine.</span><span class="sxs-lookup"><span data-stu-id="fae3e-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="fae3e-113">[Let's Encrypt](https://letsencrypt.org/)est une autorité de certificat qui offre des certificats gratuits.</span><span class="sxs-lookup"><span data-stu-id="fae3e-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="fae3e-114">Ce document utilise des [certificats de développement auto-signés](https://wikipedia.org/wiki/Self-signed_certificate) pour héberger des images pré-construites plus `localhost`.</span><span class="sxs-lookup"><span data-stu-id="fae3e-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="fae3e-115">Les instructions sont similaires à l’utilisation de certificats de production.</span><span class="sxs-lookup"><span data-stu-id="fae3e-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="fae3e-116">Pour les certificats de production :</span><span class="sxs-lookup"><span data-stu-id="fae3e-116">For production certificates:</span></span>

* <span data-ttu-id="fae3e-117">L’outil `dotnet dev-certs` n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="fae3e-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="fae3e-118">Les certificats n’ont pas besoin d’être stockés dans l’emplacement utilisé dans les instructions.</span><span class="sxs-lookup"><span data-stu-id="fae3e-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="fae3e-119">Conservez les certificats à n’importe quel endroit à l’extérieur de l’annuaire du site.</span><span class="sxs-lookup"><span data-stu-id="fae3e-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="fae3e-120">Les instructions contenues dans la section suivante `volumes` de volume de montage certificats dans les conteneurs en utilisant la propriété dans *docker-compose.yml.*</span><span class="sxs-lookup"><span data-stu-id="fae3e-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="fae3e-121">Vous pouvez ajouter des certificats `COPY` dans des images de conteneurs avec une commande dans un *Dockerfile*, mais il n’est pas recommandé.</span><span class="sxs-lookup"><span data-stu-id="fae3e-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="fae3e-122">La copie des certificats dans une image n’est pas recommandée pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="fae3e-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="fae3e-123">Il est difficile d’utiliser la même image pour les tests avec des certificats de développeur.</span><span class="sxs-lookup"><span data-stu-id="fae3e-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="fae3e-124">Il est difficile d’utiliser la même image pour l’hébergement avec des certificats de production.</span><span class="sxs-lookup"><span data-stu-id="fae3e-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="fae3e-125">Il existe un risque important de divulgation de certificats.</span><span class="sxs-lookup"><span data-stu-id="fae3e-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="fae3e-126">Démarrage d’un conteneur avec support https à l’aide de docker composer</span><span class="sxs-lookup"><span data-stu-id="fae3e-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="fae3e-127">Utilisez les instructions suivantes pour la configuration de votre système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="fae3e-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="fae3e-128">Windows à l’aide de conteneurs Linux</span><span class="sxs-lookup"><span data-stu-id="fae3e-128">Windows using Linux containers</span></span>

<span data-ttu-id="fae3e-129">Générer un certificat et configurer la machine locale :</span><span class="sxs-lookup"><span data-stu-id="fae3e-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="fae3e-130">Dans les commandes précédentes, remplacez par `{ password here }` un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="fae3e-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="fae3e-131">Créez un fichier _docker-compose.debug.yml_ avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="fae3e-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="fae3e-132">Le mot de passe spécifié dans le fichier de composition docker doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="fae3e-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="fae3e-133">Démarrer le conteneur avec ASP.NET Core configuré pour HTTPS:</span><span class="sxs-lookup"><span data-stu-id="fae3e-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="fae3e-134">macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="fae3e-134">macOS or Linux</span></span>

<span data-ttu-id="fae3e-135">Générer un certificat et configurer la machine locale :</span><span class="sxs-lookup"><span data-stu-id="fae3e-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="fae3e-136">`dotnet dev-certs https --trust`n’est pris en charge que sur macOS et Windows.</span><span class="sxs-lookup"><span data-stu-id="fae3e-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="fae3e-137">Vous devez faire confiance aux certificats sur Linux de la manière qui est pris en charge par votre distro.</span><span class="sxs-lookup"><span data-stu-id="fae3e-137">You need to trust certificates on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="fae3e-138">Il est probable que vous devez faire confiance au certificat dans votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="fae3e-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="fae3e-139">Dans les commandes précédentes, remplacez par `{ password here }` un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="fae3e-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="fae3e-140">Créez un fichier _docker-compose.debug.yml_ avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="fae3e-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="fae3e-141">Le mot de passe spécifié dans le fichier de composition docker doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="fae3e-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="fae3e-142">Démarrer le conteneur avec ASP.NET Core configuré pour HTTPS:</span><span class="sxs-lookup"><span data-stu-id="fae3e-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="fae3e-143">Fenêtres utilisant des conteneurs Windows</span><span class="sxs-lookup"><span data-stu-id="fae3e-143">Windows using Windows containers</span></span>

<span data-ttu-id="fae3e-144">Générer un certificat et configurer la machine locale :</span><span class="sxs-lookup"><span data-stu-id="fae3e-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="fae3e-145">Dans les commandes précédentes, remplacez par `{ password here }` un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="fae3e-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="fae3e-146">Créez un fichier _docker-compose.debug.yml_ avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="fae3e-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="fae3e-147">Le mot de passe spécifié dans le fichier de composition docker doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="fae3e-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="fae3e-148">Démarrer le conteneur avec ASP.NET Core configuré pour HTTPS:</span><span class="sxs-lookup"><span data-stu-id="fae3e-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
