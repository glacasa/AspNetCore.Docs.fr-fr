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
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core Web SDK

### <a name="overview"></a>Vue d’ensemble

`Microsoft.NET.Sdk.Web`est un [projet MSBuild SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) pour la construction de ASP.NET applications Core. Il est possible de construire une application ASP.NET Core sans ce SDK, cependant, le Web SDK est:

* Adapté à la fourniture d’une expérience de première classe.
* La cible recommandée pour la plupart des utilisateurs.

Utilisez le Web.SDK dans un projet :

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Fonctionnalités activées par l’utilisation du Web SDK :

* Projets ciblant .NET Core 3.0 ou plus tard implicitement référence:

  * Le [cadre commun ASP.NET Core](xref:fundamentals/metapackage-app).
  * [Analyseurs](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) conçus pour la construction de ASP.NET applications Core.
* Le Web SDK importe des cibles MSBuild qui permettent l’utilisation de profils de publication et de publication à l’aide de WebDeploy.

### <a name="properties"></a>Propriétés

| Propriété | Description |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Désactive implicitement `Microsoft.AspNetCore.App` la référence au cadre partagé. |
| `DisableImplicitAspNetCoreAnalyzers` | Désactive la référence implicite aux analyseurs de base ASP.NET. |
| `DisableImplicitComponentsAnalyzers` | Désactive implicitement la référence aux Blazor analyseurs de composants Razor lors de la création d’applications (serveur). |
