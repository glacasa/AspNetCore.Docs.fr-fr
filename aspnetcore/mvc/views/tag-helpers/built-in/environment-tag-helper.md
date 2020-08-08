---
title: Tag Helper Environnement dans ASP.NET Core
author: pkellner
description: Tag Helper Environnement ASP.NET Core défini avec toutes les propriétés
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 01fa5409136557dcbf53dbdd051bf35803e8b71f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018648"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Tag Helper Environnement dans ASP.NET Core

Par [Peter Kellner](https://peterkellner.net) et [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Le tag Helper d’environnement affiche de manière conditionnelle son contenu délimité en fonction de l' [environnement d’hébergement](xref:fundamentals/environments)actuel. L’attribut unique du Tag Helper Environnement, `names`, est une liste séparée par des virgules de noms d’environnement. Si l’un des noms d’environnement fournis correspond à l’environnement actuel, le contenu joint est affiché.

Pour avoir une vue d’ensemble de Tag Helpers, consultez <xref:mvc/views/tag-helpers/intro>.

## <a name="environment-tag-helper-attributes"></a>Attributs de Tag Helper Environnement

### <a name="names"></a>noms

`names` accepte un seul nom d’environnement d’hébergement ou une liste séparée par des virgules de noms d’environnement d’hébergement qui déclenchent l’affichage du contenu joint.

Les valeurs d’environnement sont comparées à la valeur actuelle retournée par [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*). La comparaison ignore la casse.

L’exemple suivant utilise un Tag Helper Environnement. Le contenu est affiché si l’environnement d’hébergement est un environnement de préproduction (Staging) ou de production :

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>Attributs include et exclude

`include`les & `exclude` attributs contrôlent le rendu du contenu délimité en fonction des noms d’environnement d’hébergement inclus ou exclus.

### <a name="include"></a>include

La propriété `include` présente un comportement similaire à l’attribut `names`. Un environnement listé dans la valeur d’attribut `include` doit correspondre à l’environnement d’hébergement de l’application ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) pour afficher le contenu de la balise `<environment>`.

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>exclure

Contrairement à l’attribut `include`, le contenu de la balise `<environment>` est affiché quand l’environnement d’hébergement ne correspond pas à un environnement listé dans la valeur d’attribut `exclude`.

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/environments>
