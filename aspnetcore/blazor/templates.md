---
titre : « ASP.NET Core Blazor Templates » auteur : Description : « en savoir plus sur Blazor les modèles d’application ASP.net Core et la Blazor structure de projet ».
monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

---
# <a name="aspnet-core-blazor-templates"></a>Modèles de ASP.NET Core Blazor

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

L' Blazor infrastructure fournit des modèles pour développer des applications pour chacun des Blazor modèles d’hébergement :

* BlazorWebassembly ( `blazorwasm` )
* BlazorServer ( `blazorserver` )

Pour plus d’informations sur les Blazor modèles d’hébergement de, consultez <xref:blazor/hosting-models> .

Pour obtenir des instructions pas à pas sur la création Blazor d’une application à partir d’un modèle, consultez <xref:blazor/get-started> .

Les options de modèle sont disponibles en passant l' `--help` option à la commande [dotnet New](/dotnet/core/tools/dotnet-new) CLI :

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazorstructure de projet

Les fichiers et dossiers suivants composent une Blazor application générée à partir d’un Blazor modèle :

* *Program.cs* &ndash; le point d’entrée de l’application qui installe les éléments suivants :

  * [Hôte](xref:fundamentals/host/generic-host) ASP.net Core ( Blazor serveur)
  * Hôte webassembly ( Blazor Webassembly) &ndash; le code de ce fichier est propre aux applications créées à partir du Blazor modèle webassembly ( `blazorwasm` ).
    * Le `App` composant, qui est le composant racine de l’application, est spécifié en tant qu' `app` élément DOM de la `Add` méthode.
    * Les services peuvent être configurés avec la `ConfigureServices` méthode sur le générateur de l’hôte (par exemple, `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).
    * La configuration peut être fournie par le biais du générateur d’ordinateur hôte ( `builder.Configuration` ).

* *Startup.cs* ( Blazor Server) &ndash; contient la logique de démarrage de l’application. La `Startup` classe définit deux méthodes :

  * `ConfigureServices`&ndash;Configure les services d' [injection de dépendances](xref:fundamentals/dependency-injection) de l’application. Dans les Blazor applications serveur, les services sont ajoutés en appelant <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> , et le `WeatherForecastService` est ajouté au conteneur de service pour une utilisation par l’exemple de `FetchData` composant.
  * `Configure`&ndash;Configure le pipeline de traitement des demandes de l’application :
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>est appelé pour configurer un point de terminaison pour la connexion en temps réel avec le navigateur. La connexion est créée avec [SignalR](xref:signalr/introduction) , qui est une infrastructure permettant d’ajouter des fonctionnalités Web en temps réel aux applications.
    * [MapFallbackToPage (« /_Host »)](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) est appelé pour configurer la page racine de l’application (*pages/_Host. cshtml*) et activer la navigation.

* *wwwroot/index.html* ( Blazor webassembly) &ndash; page racine de l’application implémentée en tant que page HTML :
  * Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.
  * La page spécifie l’emplacement où le `App` composant racine est restitué. Le `App` composant (*app. Razor*) est spécifié en tant qu' `app` élément DOM de la `AddComponent` méthode dans `Startup.Configure` .
  * Le `_framework/blazor.webassembly.js` fichier JavaScript est chargé, ce qui suit :
    * Télécharge le Runtime .NET, l’application et les dépendances de l’application.
    * Initialise le runtime pour exécuter l’application.

* *App. Razor* &ndash; le composant racine de l’application qui configure le routage côté client à l’aide du <xref:Microsoft.AspNetCore.Components.Routing.Router> composant. Le `Router` composant intercepte la navigation dans le navigateur et affiche la page qui correspond à l’adresse demandée.

* Le dossier *pages* &ndash; contient les composants/pages routables (*. Razor*) qui composent l' Blazor application et la Razor page racine d’une Blazor application serveur. L’itinéraire de chaque page est spécifié à l’aide de la [`@page`](xref:mvc/views/razor#page) directive. Le modèle comprend les éléments suivants :
  * *_Host. cshtml* ( Blazor Server) &ndash; la page racine de l’application est implémentée en tant que Razor page :
    * Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.
    * Le `_framework/blazor.server.js` fichier JavaScript est chargé, ce qui configure la connexion en temps réel SignalR entre le navigateur et le serveur.
    * La page hôte spécifie où le `App` composant racine (*app. Razor*) est affiché.
  * `Counter`(*Counter. Razor*) &ndash; implémente la page de compteur.
  * `Error`(*Error. Razor*, Blazor Application serveur uniquement) &ndash; rendu lorsqu’une exception non gérée se produit dans l’application.
  * `FetchData`(*FetchData. Razor*) &ndash; implémente la page FETCH Data.
  * `Index`(*Index. Razor*) &ndash; implémente la page d’hébergement.

* Le dossier *partagé* &ndash; contient d’autres composants d’interface utilisateur (*. Razor*) utilisés par l’application :
  * `MainLayout`(*MainLayout. Razor*) &ndash; composant de disposition de l’application.
  * `NavMenu`(*NavMenu. Razor*) &ndash; implémente la navigation dans l’encadré. Comprend le [composant NavLink](xref:blazor/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), qui affiche des liens de navigation vers d’autres Razor composants. Le `NavLink` composant indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.

* *_Imports. Razor* &ndash; Inclut des Razor directives communes à inclure dans les composants de l’application (*. Razor*), tels que les [`@using`](xref:mvc/views/razor#using) directives pour les espaces de noms.

* Dossier de *données* ( Blazor serveur) &ndash; contient la `WeatherForecast` classe et l’implémentation du `WeatherForecastService` qui fournissent des exemples de données météorologiques au composant de l’application `FetchData` .

* *wwwroot* &ndash; Dossier [racine Web](xref:fundamentals/index#web-root) de l’application contenant les ressources statiques publiques de l’application.

* paramètres de configuration *appSettings. JSON* ( Blazor serveur) &ndash; pour l’application.
