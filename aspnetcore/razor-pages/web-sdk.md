---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Aperçu de Microsoft.NET.Sdk.Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661055"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="b2514-103">ASP.NET Core Web SDK</span><span class="sxs-lookup"><span data-stu-id="b2514-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="b2514-104">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="b2514-104">Overview</span></span>

<span data-ttu-id="b2514-105">`Microsoft.NET.Sdk.Web`est un [projet MSBuild SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) pour la construction de ASP.NET applications Core.</span><span class="sxs-lookup"><span data-stu-id="b2514-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="b2514-106">Il est possible de construire une application ASP.NET Core sans ce SDK, cependant, le Web SDK est:</span><span class="sxs-lookup"><span data-stu-id="b2514-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="b2514-107">Adapté à la fourniture d’une expérience de première classe.</span><span class="sxs-lookup"><span data-stu-id="b2514-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="b2514-108">La cible recommandée pour la plupart des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="b2514-108">The recommended target for most users.</span></span>

<span data-ttu-id="b2514-109">Utilisez le Web.SDK dans un projet :</span><span class="sxs-lookup"><span data-stu-id="b2514-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="b2514-110">Fonctionnalités activées par l’utilisation du Web SDK :</span><span class="sxs-lookup"><span data-stu-id="b2514-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="b2514-111">Projets ciblant .NET Core 3.0 ou plus tard implicitement référence:</span><span class="sxs-lookup"><span data-stu-id="b2514-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="b2514-112">Le [cadre commun ASP.NET Core](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b2514-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="b2514-113">[Analyseurs](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) conçus pour la construction de ASP.NET applications Core.</span><span class="sxs-lookup"><span data-stu-id="b2514-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="b2514-114">Le Web SDK importe des cibles MSBuild qui permettent l’utilisation de profils de publication et de publication à l’aide de WebDeploy.</span><span class="sxs-lookup"><span data-stu-id="b2514-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="b2514-115">Propriétés</span><span class="sxs-lookup"><span data-stu-id="b2514-115">Properties</span></span>

| <span data-ttu-id="b2514-116">Propriété</span><span class="sxs-lookup"><span data-stu-id="b2514-116">Property</span></span> | <span data-ttu-id="b2514-117">Description</span><span class="sxs-lookup"><span data-stu-id="b2514-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="b2514-118">Désactive implicitement `Microsoft.AspNetCore.App` la référence au cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="b2514-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="b2514-119">Désactive la référence implicite aux analyseurs de base ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="b2514-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="b2514-120">Désactive implicitement la référence aux Blazor analyseurs de composants Razor lors de la création d’applications (serveur).</span><span class="sxs-lookup"><span data-stu-id="b2514-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
