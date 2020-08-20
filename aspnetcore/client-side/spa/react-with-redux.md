---
title: Utiliser le modèle de projet React-with-Redux avec ASP.NET Core
author: SteveSandersonMS
description: Découvrez comment démarrer avec le modèle de projet d’application monopage ASP.NET Core pour React-with-Redux et create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
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
uid: spa/react-with-redux
ms.openlocfilehash: ac5b5d7161e79f133a1f107251fa6707b752c568
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628727"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="f47c4-103">Utiliser le modèle de projet React-with-Redux avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f47c4-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="f47c4-104">Le modèle de projet React-with-Redux mis à jour fournit un point de départ pratique pour les applications ASP.NET Core utilisant les conventions React, Redux et [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) pour implémenter une interface utilisateur (IU) côté client enrichie.</span><span class="sxs-lookup"><span data-stu-id="f47c4-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="f47c4-105">À l’exception de la commande de création de projet, toutes les informations sur le modèle React-with-Redux sont les mêmes que celles relatives au modèle React.</span><span class="sxs-lookup"><span data-stu-id="f47c4-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="f47c4-106">Pour créer ce type de projet, exécutez `dotnet new reactredux` au lieu de `dotnet new react`.</span><span class="sxs-lookup"><span data-stu-id="f47c4-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="f47c4-107">Pour plus d’informations sur les fonctionnalités communes aux deux modèles basés sur React, consultez la [documentation relative aux modèles React](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="f47c4-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="f47c4-108">Pour plus d’informations sur la configuration d’une sous-application react-with-Redux dans IIS, consultez le [modèle ReactRedux 2,1 : impossible d’utiliser Spa sur IIS (ASPNET/Templating &num; 555)](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="f47c4-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
