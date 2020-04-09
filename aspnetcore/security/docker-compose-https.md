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
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Hébergement ASP.NET images de base avec Docker Compose sur HTTPS


ASP.NET Core utilise [HTTPS par défaut](/aspnet/core/security/enforcing-ssl). [HTTPS](https://en.wikipedia.org/wiki/HTTPS) s’appuie sur [des certificats](https://en.wikipedia.org/wiki/Public_key_certificate) de confiance, d’identité et de cryptage.

Ce document explique comment exécuter des images de conteneurs pré-construites avec HTTPS.

Voir [Développer ASP.NET applications de base avec Docker sur HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) pour les scénarios de développement.

Cet échantillon nécessite [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) ou plus tard du [client Docker](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Prérequis

Le [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) ou plus tard est nécessaire pour certaines des instructions contenues dans ce document.

## <a name="certificates"></a>Certificats

Un certificat d’une autorité de [certificat](https://wikipedia.org/wiki/Certificate_authority) est requis pour [l’hébergement de production](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) pour un domaine. [Let's Encrypt](https://letsencrypt.org/)est une autorité de certificat qui offre des certificats gratuits.

Ce document utilise des [certificats de développement auto-signés](https://wikipedia.org/wiki/Self-signed_certificate) pour héberger des images pré-construites plus `localhost`. Les instructions sont similaires à l’utilisation de certificats de production.

Pour les certificats de production :

* L’outil `dotnet dev-certs` n’est pas nécessaire.
* Les certificats n’ont pas besoin d’être stockés dans l’emplacement utilisé dans les instructions. Conservez les certificats à n’importe quel endroit à l’extérieur de l’annuaire du site.

Les instructions contenues dans la section suivante `volumes` de volume de montage certificats dans les conteneurs en utilisant la propriété dans *docker-compose.yml.* Vous pouvez ajouter des certificats `COPY` dans des images de conteneurs avec une commande dans un *Dockerfile*, mais il n’est pas recommandé. La copie des certificats dans une image n’est pas recommandée pour les raisons suivantes :

* Il est difficile d’utiliser la même image pour les tests avec des certificats de développeur.
* Il est difficile d’utiliser la même image pour l’hébergement avec des certificats de production.
* Il existe un risque important de divulgation de certificats.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Démarrage d’un conteneur avec support https à l’aide de docker composer

Utilisez les instructions suivantes pour la configuration de votre système d’exploitation.

### <a name="windows-using-linux-containers"></a>Windows à l’aide de conteneurs Linux

Générer un certificat et configurer la machine locale :

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Dans les commandes précédentes, remplacez par `{ password here }` un mot de passe.

Créez un fichier _docker-compose.debug.yml_ avec le contenu suivant :

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
Le mot de passe spécifié dans le fichier de composition docker doit correspondre au mot de passe utilisé pour le certificat.

Démarrer le conteneur avec ASP.NET Core configuré pour HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS ou Linux

Générer un certificat et configurer la machine locale :

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`n’est pris en charge que sur macOS et Windows. Vous devez faire confiance aux certificats sur Linux de la manière qui est pris en charge par votre distro. Il est probable que vous devez faire confiance au certificat dans votre navigateur.

Dans les commandes précédentes, remplacez par `{ password here }` un mot de passe.

Créez un fichier _docker-compose.debug.yml_ avec le contenu suivant :

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
Le mot de passe spécifié dans le fichier de composition docker doit correspondre au mot de passe utilisé pour le certificat.

Démarrer le conteneur avec ASP.NET Core configuré pour HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Fenêtres utilisant des conteneurs Windows

Générer un certificat et configurer la machine locale :

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Dans les commandes précédentes, remplacez par `{ password here }` un mot de passe.

Créez un fichier _docker-compose.debug.yml_ avec le contenu suivant :

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
Le mot de passe spécifié dans le fichier de composition docker doit correspondre au mot de passe utilisé pour le certificat.

Démarrer le conteneur avec ASP.NET Core configuré pour HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
