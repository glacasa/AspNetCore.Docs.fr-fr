---
title: Acquisition de bibliothèque côté client dans ASP.NET Core avec LibMan
author: scottaddie
description: Découvrez comment installer des ressources de bibliothèque côté client dans un projet ASP.NET Core à l’aide du Gestionnaire de bibliothèque (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
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
uid: client-side/libman/index
ms.openlocfilehash: 8458f2fe1cdc139a3f22c7d73675301d89ad368e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625633"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>Acquisition de bibliothèque côté client dans ASP.NET Core avec LibMan

Par [Scott Addie](https://twitter.com/Scott_Addie)

Le Gestionnaire de bibliothèque (LibMan) est un outil allégé d’acquisition de bibliothèque côté client. LibMan télécharge des bibliothèques et infrastructures populaires à partir du système de fichiers ou d’un [réseau de distribution de contenu (CDN)](https://wikipedia.org/wiki/Content_delivery_network). Les CDN pris en charge incluent [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)et [unpkg](https://unpkg.com/#/). Les fichiers de bibliothèque sélectionnés sont récupérés et placés à l’emplacement approprié dans le projet ASP.NET Core.

## <a name="libman-use-cases"></a>Cas d’usage de LibMan

LibMan offre les avantages suivants :

* Seuls les fichiers de bibliothèque dont vous avez besoin sont téléchargés.
* Des outils supplémentaires, tels que [Node.js](https://nodejs.org), [npm](https://www.npmjs.com) et [WebPack](https://webpack.js.org), ne sont pas nécessaires pour acquérir un sous-ensemble de fichiers dans une bibliothèque.
* Les fichiers peuvent être placés à un emplacement spécifique sans avoir recours à des tâches de génération ou à la copie manuelle de fichiers.

Pour plus d’informations sur les avantages offerts par LibMan, regardez la vidéo intitulée [Modern front-end web development in Visual Studio 2017 : LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).

LibMan n’est pas un système de gestion de packages. Si vous utilisez déjà un gestionnaire de package, tel que npm ou [yarn](https://yarnpkg.com), continuez ainsi. LibMan n’a pas été développé pour remplacer ces outils.

## <a name="additional-resources"></a>Ressources complémentaires

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [Dépôt GitHub LibMan](https://github.com/aspnet/LibraryManager)
