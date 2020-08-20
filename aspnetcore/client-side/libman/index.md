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
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="d639c-103">Acquisition de bibliothèque côté client dans ASP.NET Core avec LibMan</span><span class="sxs-lookup"><span data-stu-id="d639c-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="d639c-104">Par [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d639c-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d639c-105">Le Gestionnaire de bibliothèque (LibMan) est un outil allégé d’acquisition de bibliothèque côté client.</span><span class="sxs-lookup"><span data-stu-id="d639c-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="d639c-106">LibMan télécharge des bibliothèques et infrastructures populaires à partir du système de fichiers ou d’un [réseau de distribution de contenu (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span><span class="sxs-lookup"><span data-stu-id="d639c-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="d639c-107">Les CDN pris en charge incluent [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)et [unpkg](https://unpkg.com/#/).</span><span class="sxs-lookup"><span data-stu-id="d639c-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="d639c-108">Les fichiers de bibliothèque sélectionnés sont récupérés et placés à l’emplacement approprié dans le projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d639c-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="d639c-109">Cas d’usage de LibMan</span><span class="sxs-lookup"><span data-stu-id="d639c-109">LibMan use cases</span></span>

<span data-ttu-id="d639c-110">LibMan offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="d639c-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="d639c-111">Seuls les fichiers de bibliothèque dont vous avez besoin sont téléchargés.</span><span class="sxs-lookup"><span data-stu-id="d639c-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="d639c-112">Des outils supplémentaires, tels que [Node.js](https://nodejs.org), [npm](https://www.npmjs.com) et [WebPack](https://webpack.js.org), ne sont pas nécessaires pour acquérir un sous-ensemble de fichiers dans une bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="d639c-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="d639c-113">Les fichiers peuvent être placés à un emplacement spécifique sans avoir recours à des tâches de génération ou à la copie manuelle de fichiers.</span><span class="sxs-lookup"><span data-stu-id="d639c-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="d639c-114">Pour plus d’informations sur les avantages offerts par LibMan, regardez la vidéo intitulée [Modern front-end web development in Visual Studio 2017 : LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span><span class="sxs-lookup"><span data-stu-id="d639c-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="d639c-115">LibMan n’est pas un système de gestion de packages.</span><span class="sxs-lookup"><span data-stu-id="d639c-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="d639c-116">Si vous utilisez déjà un gestionnaire de package, tel que npm ou [yarn](https://yarnpkg.com), continuez ainsi.</span><span class="sxs-lookup"><span data-stu-id="d639c-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="d639c-117">LibMan n’a pas été développé pour remplacer ces outils.</span><span class="sxs-lookup"><span data-stu-id="d639c-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d639c-118">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="d639c-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="d639c-119">Dépôt GitHub LibMan</span><span class="sxs-lookup"><span data-stu-id="d639c-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
