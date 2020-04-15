---
title: Intégrer les composants ASP.NET Core Razor dans les applications Razor Pages et MVC
author: guardrex
description: Renseignez-vous sur les scénarios de Blazor liaison de données pour les composants et les éléments DOM dans les applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: c242fbef70d289929d5c005abc0aa431619862b3
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383968"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Intégrer les composants ASP.NET Core Razor dans les applications Razor Pages et MVC

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Les composants Razor peuvent être intégrés dans les pages Razor et les applications MVC. Lorsque la page ou la vue est rendue, les composants peuvent être prédilués en même temps.

Après [la préparation de l’application,](#prepare-the-app)utilisez les conseils dans les sections suivantes en fonction des exigences de l’application :

* Composants routables &ndash; Pour les composants qui sont directement routables des demandes d’utilisateur. Suivez ces conseils lorsque les visiteurs doivent être en mesure de [`@page`](xref:mvc/views/razor#page) faire une demande HTTP dans leur navigateur pour un composant avec une directive.
  * [Utilisez des composants routables dans une application Razor Pages](#use-routable-components-in-a-razor-pages-app)
  * [Utilisez des composants routables dans une application MVC](#use-routable-components-in-an-mvc-app)
* [Rendre les composants à partir d’une page ou d’une vue](#render-components-from-a-page-or-view) &ndash; Pour les composants qui ne sont pas directement routables des demandes des utilisateurs. Suivez ces conseils lorsque l’application intègre des composants dans les pages et les vues existantes avec [l’assistant d’étiquette de composant](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="prepare-the-app"></a>Préparer l’application

Une application existante Razor Pages ou MVC peut intégrer des composants Razor dans les pages et les vues :

1. Dans le fichier de mise en page de l’application (*_Layout.cshtml )*:

   * Ajouter l’étiquette `<base>` `<head>` suivante à l’élément :

     ```html
     <base href="~/" />
     ```

     La `href` valeur (le *chemin de base de l’application*) dans l’exemple précédent suppose que l’application réside sur le chemin de l’URL racine (`/`). Si l’application est une sous-application, suivez les conseils <xref:host-and-deploy/blazor/index#app-base-path> dans la section du chemin de base de l’application de l’article. *App base path*

     Le fichier *_Layout.cshtml* est situé dans le dossier *Pages/Shared* dans une application Razor Pages ou un dossier *Vues/Partage* dans une application MVC.

   * Ajoutez `<script>` une balise pour le script *blazor.server.js* immédiatement avant l’étiquette de clôture `</body>` :

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Le cadre ajoute le script *blazor.server.js* à l’application. Il n’est pas nécessaire d’ajouter manuellement le script à l’application.

1. Ajoutez un fichier *_Imports.razor* au dossier racine du projet avec le contenu suivant `MyAppNamespace`(changer l’espace de nom de famille, , à l’espace nom de l’application):

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. Dans `Startup.ConfigureServices`, enregistrez le service Blazor Server :

   ```csharp
   services.AddServerSideBlazor();
   ```

1. Dans `Startup.Configure`, ajouter le point de `app.UseEndpoints`terminaison Blazor Hub à :

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Intégrer les composants dans n’importe quelle page ou vue. Pour plus d’informations, consultez les [composants Render à partir d’une page ou d’une](#render-components-from-a-page-or-view) section de vue.

## <a name="use-routable-components-in-a-razor-pages-app"></a>Utilisez des composants routables dans une application Razor Pages

*Cette section concerne l’ajout de composants qui sont directement routables des demandes des utilisateurs.*

Pour prendre en charge les composants Razor routables dans les applications Razor Pages :

1. Suivez les conseils dans la section [Préparer l’application.](#prepare-the-app)

1. Ajoutez un fichier *App.razor* à la racine du projet avec le contenu suivant :

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Ajoutez un fichier *_Host.cshtml* au dossier *Pages* avec le contenu suivant :

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Les composants utilisent le fichier *_Layout.cshtml* partagé pour leur mise en page.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configure si `App` le composant :

   * Est préditulé dans la page.
   * Est rendu comme HTML statique sur la page ou si elle inclut les informations nécessaires pour bootstrap une application Blazor de l’agent utilisateur.

   | Mode Rendu | Description |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Rend le `App` composant en HTML statique et inclut un marqueur pour une application Blazor Server. Lorsque l’agent utilisateur démarre, ce marqueur est utilisé pour bootstrap une application Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Rend un marqueur pour une application Blazor Server. La sortie `App` du composant n’est pas incluse. Lorsque l’agent utilisateur démarre, ce marqueur est utilisé pour bootstrap une application Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Rend le `App` composant en HTML statique. |

   Pour plus d’informations sur l’aide à l’étiquette de composant, voir <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

1. Ajoutez un itinéraire de faible priorité pour la page *_Host.cshtml* à la configuration de point de terminaison dans `Startup.Configure`:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Ajoutez des composants routables à l’application. Par exemple :

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Pour plus d’informations sur les espaces de nom, consultez la section [Espaces de noms de composant.](#component-namespaces)

## <a name="use-routable-components-in-an-mvc-app"></a>Utilisez des composants routables dans une application MVC

*Cette section concerne l’ajout de composants qui sont directement routables des demandes des utilisateurs.*

Pour prendre en charge les composants Razor routables dans les applications MVC :

1. Suivez les conseils dans la section [Préparer l’application.](#prepare-the-app)

1. Ajoutez un fichier *App.razor* à la racine du projet avec le contenu suivant :

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Ajoutez un fichier *_Host.cshtml* au dossier *Vues/Accueil* avec le contenu suivant :

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Les composants utilisent le fichier *_Layout.cshtml* partagé pour leur mise en page.
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configure si `App` le composant :

   * Est préditulé dans la page.
   * Est rendu comme HTML statique sur la page ou si elle inclut les informations nécessaires pour bootstrap une application Blazor de l’agent utilisateur.

   | Mode Rendu | Description |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Rend le `App` composant en HTML statique et Blazor inclut un marqueur pour une application Server. Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Rend un marqueur Blazor pour une application Server. La sortie `App` du composant n’est pas incluse. Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Rend le `App` composant en HTML statique. |

   Pour plus d’informations sur l’aide à l’étiquette de composant, voir <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

1. Ajoutez une action au contrôleur à domicile :

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Ajoutez un itinéraire peu prioritaire pour l’action du contrôleur qui renvoie la vue `Startup.Configure` *_Host.cshtml* à la configuration du point de terminaison dans :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Créez un dossier *Pages* et ajoutez des composants routables à l’application. Par exemple :

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Pour plus d’informations sur les espaces de nom, consultez la section [Espaces de noms de composant.](#component-namespaces)

## <a name="render-components-from-a-page-or-view"></a>Rendre les composants à partir d’une page ou d’une vue

*Cette section concerne l’ajout de composants aux pages ou aux vues, où les composants ne sont pas directement routables des demandes des utilisateurs.*

Pour rendre un composant à partir d’une page ou d’une vue, utilisez [l’aide à l’étiquette de composant](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

Pour plus d’informations sur la façon dont `Component` les composants sont rendus, l’état des composants et l’aide Tag, voir les articles suivants:

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>

## <a name="component-namespaces"></a>Espaces de noms de composant

Lorsque vous utilisez un dossier personnalisé pour contenir les composants de l’application, ajoutez l’espace de nom représentant le dossier à la page/vue ou au fichier *_ViewImports.cshtml.* Dans l’exemple suivant :

* Changez `MyAppNamespace` pour l’espace nom de l’application.
* Si un dossier nommé *Composants* n’est pas utilisé `Components` pour contenir les composants, changez-vous au dossier où résident les composants.

```cshtml
@using MyAppNamespace.Components
```

Le fichier *_ViewImports.cshtml* est situé dans le dossier *Pages* d’une application Razor Pages ou dans le dossier *Vues* d’une application MVC.

Pour plus d’informations, consultez <xref:blazor/components#import-components>.
