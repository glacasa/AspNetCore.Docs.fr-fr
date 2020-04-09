---
title: Contrôles d’intégrité dans ASP.NET Core
author: rick-anderson
description: Découvrez comment configurer des contrôles d’intégrité pour l’infrastructure ASP.NET Core, comme des applications ou des bases de données.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 314e55c818cddf1dad2e3ec74d4d1e041ce7366f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664884"
---
# <a name="health-checks-in-aspnet-core"></a>Contrôles d’intégrité dans ASP.NET Core

Par [Glenn Condron](https://github.com/glennc)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core offre des contrôles de santé Middleware et des bibliothèques pour signaler la santé des composants de l’infrastructure des applications.

Les contrôles d’intégrité sont exposés par une application comme des points de terminaison HTTP. Les points de terminaison de vérification d’intégrité peuvent être configurés pour de nombreux scénarios de supervision en temps réel :

* Les sondes d’intégrité peuvent être utilisées par les orchestrateurs de conteneurs et les équilibreurs de charge afin de vérifier l’état d’une application. Par exemple, un orchestrateur de conteneurs peut répondre à un résultat de non intégrité en arrêtant le déploiement ou en redémarrant un conteneur. Face à une application non saine, l’équilibreur de charge peut réagir en redirigeant le trafic vers une instance saine.
* L’utilisation de la mémoire, des disques et des autres ressources de serveur physique peut être supervisée dans le cadre d’un contrôle d’intégrité.
* Les contrôles d’intégrité peuvent tester les dépendances d’une application, telles que les bases de données et les points de terminaison de service externes, dans le but de vérifier leur disponibilité et leur bon fonctionnement.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

L’exemple d’application comprend des exemples pour les scénarios décrits dans cette rubrique. Pour exécuter l’exemple d’application selon un scénario donné, utilisez la commande [dotnet run](/dotnet/core/tools/dotnet-run) dans un interpréteur de commandes, à partir du dossier du projet. Pour plus d’informations sur l’utilisation de l’exemple d’application, consultez le fichier *README.md* de l’exemple d’application ainsi que les descriptions de scénarios de cette rubrique.

## <a name="prerequisites"></a>Prérequis

Les contrôles d’intégrité sont généralement utilisés avec un service de supervision externe ou un orchestrateur de conteneurs pour vérifier l’état d’une application. Avant d’ajouter des contrôles d’intégrité à une application, vous devez décider du système de supervision à utiliser. Le système de supervision détermine les types de contrôles d’intégrité qui doivent être créés ainsi que la configuration de leurs points de terminaison.

Le package [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) est référencé implicitement pour ASP.NET applications Core. Pour effectuer des contrôles de santé à l’aide de l’entité Framework Core, ajoutez une référence globale au package [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore.](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore)

L’exemple d’application fournit du code de démarrage pour illustrer les contrôles d’intégrité de plusieurs scénarios. Le scénario [probe de la base de données](#database-probe) vérifie l’intégrité d’une connexion de base de données à l’aide d’[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Le scénario [Sondage DbContext](#entity-framework-core-dbcontext-probe) vérifie une base de données à l’aide d’un `DbContext` EF Core. Pour explorer les scénarios de base de données, l’exemple d’application :

* Crée une base de données et fournit sa chaîne de connexion dans le fichier *appsettings.json*.
* Possède les références de package suivantes dans son fichier de projet :
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas maintenu ou pris en charge par Microsoft.

Un autre scénario de contrôle d’intégrité montre comment filtrer des contrôles d’intégrité selon un port de gestion. L’exemple d’application vous oblige à créer un fichier *Properties/launchSettings.json* comprenant l’URL de gestion et le port de gestion. Pour plus d’informations, consultez la section [Filtrer par port](#filter-by-port).

## <a name="basic-health-probe"></a>Sondage d’intégrité de base

Pour de nombreuses applications, un sondage d’intégrité de base qui signale la disponibilité d’une application pour le traitement des requêtes (*liveness*) suffit à découvrir l’état de l’application.

La configuration de base enregistre les services de bilan de santé et appelle le Health Checks Middleware pour répondre à un point de terminaison URL avec une réponse sanitaire. Par défaut, aucun contrôle d’intégrité n’est inscrit pour tester les dépendances ou le sous-système. L’application est considérée comme saine si elle est capable de répondre à l’URL de point de terminaison de contrôle d’intégrité. L’enregistreur de réponse par défaut écrit l’état (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) sous forme de texte en clair qu’il renvoie au client, indiquant si l’état est [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) ou [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. Créez un point de `MapHealthChecks` terminaison de bilan de santé en appelant dedans `Startup.Configure`.

Dans l’exemple d’application, le point de terminaison de contrôle d’intégrité est créé au niveau de `/health` (*BasicStartup.cs*) :

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

Pour exécuter le scénario de configuration de base à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Exemple Docker

[Docker](xref:host-and-deploy/docker/index) fournit une directive `HEALTHCHECK` intégrée qui peut être utilisée pour vérifier l’état d’une application utilisant la configuration de contrôle d’intégrité de base :

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Créer des contrôles d’intégrité

Les contrôles d’intégrité sont créés via l’implémentation de l’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>. La méthode <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> retourne un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> qui indique l’état d’intégrité comme étant `Healthy`, `Degraded` ou `Unhealthy`. Le résultat est écrit sous la forme de texte en clair avec un code d’état configurable (la configuration est décrite dans la section [Options de contrôle d’intégrité](#health-check-options)). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> peut également retourner des paires clé-valeur facultatives.

La `ExampleHealthCheck` classe suivante démontre la disposition d’un bilan de santé. La logique des contrôles `CheckHealthAsync` de santé est placée dans la méthode. L’exemple suivant définit une `healthCheckResultHealthy`variable `true`factice, , à . Si la `healthCheckResultHealthy` valeur de `false`est réglé à , le statut [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) est retourné.

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a>Inscrire les services de contrôle d’intégrité

Le `ExampleHealthCheck` type est ajouté aux <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices`services de bilan de santé avec dans :

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

La surcharge <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> de l’exemple suivant définit l’état d’échec (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) de manière à être signalé lorsque le contrôle d’intégrité signale un échec. Si l’état d’échec est défini sur `null` (par défaut), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) est signalé. Cette surcharge est utile pour les créateurs de bibliothèque, dans les cas où l’état d’échec indiqué par la bibliothèque est appliqué par l’application lorsqu’un échec est signalé par le contrôle d’intégrité, si l’implémentation de ce dernier respecte le paramètre.

Des *étiquettes* peuvent être utilisées pour filtrer les contrôles d’intégrité (cette procédure est décrite plus loin dans la section [Filtrer les contrôles d’intégrité](#filter-health-checks)).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> peut également exécuter une fonction lambda. Dans l’exemple suivant, le nom du contrôle d’intégrité est spécifié en tant que `Example`, et la vérification retourne toujours un état sain :

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

Appelez <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> pour transmettre des arguments à une mise en œuvre de bilan de santé. Dans l’exemple `TestHealthCheckWithArgs` suivant, accepte un entier et <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> une chaîne pour une utilisation lorsqu’on l’appelle :

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

`TestHealthCheckWithArgs`est enregistré `AddTypeActivatedCheck` en appelant avec l’integer et la chaîne passée à la mise en œuvre:

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a>Utiliser les contrôles de santé Routing

Dans `Startup.Configure`, `MapHealthChecks` appelez le constructeur de point de terminaison avec l’URL de point de terminaison ou le chemin relatif :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a>Exiger l’hôte

Appelez `RequireHost` pour spécifier un ou plusieurs hôtes autorisés pour le point de terminaison de bilan de santé. Les hôtes doivent être Unicode plutôt que chétifcode et peuvent inclure un port. Si une collection n’est pas fournie, un hôte est accepté.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

Pour plus d’informations, consultez la section [Filtrer par port](#filter-by-port).

### <a name="require-authorization"></a>Exiger l’autorisation

Appelez `RequireAuthorization` pour exécuter Authorization Middleware sur le point de terminaison de demande de bilan de santé. Une `RequireAuthorization` surcharge accepte une ou plusieurs politiques d’autorisation. Si une stratégie n’est pas fournie, la stratégie d’autorisation par défaut est utilisée.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a>Activer les requêtes d’origines différentes

Bien que l’exécution manuelle des contrôles de santé à partir d’un `RequireCors` navigateur n’est pas un scénario d’utilisation courante, CORS Middleware peut être activé en faisant appel à des points de terminaison de bilan de santé. Une `RequireCors` surcharge accepte un délégué constructeur`CorsPolicyBuilder`de politiques CORS ( ) ou un nom de politique. Si une stratégie n’est pas fournie, la stratégie CORS par défaut est utilisée. Pour plus d’informations, consultez <xref:security/cors>.

## <a name="health-check-options"></a>Options de contrôle d’intégrité

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> permet de personnaliser le comportement du contrôle d’intégrité :

* [Filtrer les contrôles d’intégrité](#filter-health-checks)
* [Personnaliser le code d’état HTTP](#customize-the-http-status-code)
* [Supprimer les en-têtes de cache](#suppress-cache-headers)
* [Personnaliser la sortie](#customize-output)

### <a name="filter-health-checks"></a>Filtrer les contrôles d’intégrité

Par défaut, Health Checks Middleware exécute tous les contrôles de santé enregistrés. Pour exécuter un sous-ensemble de contrôles d’intégrité, fournissez une fonction qui retourne une valeur booléenne à l’option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>. Dans l’exemple suivant, le contrôle d’intégrité `Bar` est filtré en fonction de son étiquette (`bar_tag`) dans l’instruction conditionnelle de la fonction, où `true` est retourné uniquement si la propriété <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> du contrôle d’intégrité correspond à `foo_tag` ou à `baz_tag` :

Dans `Startup.ConfigureServices` :

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

Dans `Startup.Configure`, `Predicate` les filtres sur le bilan de santé 'Bar'. Seuls Foo et Baz exécutent.:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a>Personnaliser le code d’état HTTP

Utilisez <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> pour personnaliser le mappage de l’état d’intégrité aux codes d’état HTTP. Les affectations <xref:Microsoft.AspNetCore.Http.StatusCodes> suivantes sont les valeurs par défaut utilisées par le middleware. Vous pouvez modifier les valeurs de code d’état selon vos besoins.

Dans `Startup.Configure` :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a>Supprimer les en-têtes de cache

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>contrôle si le Health Checks Middleware ajoute des en-têtes HTTP à une réponse de sonde pour empêcher la mise en cache de réponse. Si la valeur est `false` (par défaut), le middleware définit ou substitue les en-têtes `Cache-Control`, `Expires` et `Pragma` afin d’empêcher la mise en cache de la réponse. Si la valeur est `true`, le middleware ne modifie pas les en-têtes de cache de la réponse.

Dans `Startup.Configure` :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a>Personnaliser la sortie

Dans `Startup.Configure`, définir l’option [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) à un délégué pour la rédaction de la réponse:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

Le délégué par défaut écrit une réponse minimale constituée de texte en clair, avec la valeur de chaîne [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Les délégués personnalisés suivants extrader une réponse JSON personnalisée.

Le premier exemple de l’application <xref:System.Text.Json?displayProperty=fullName>d’échantillon démontre comment utiliser :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

Le deuxième exemple montre comment utiliser [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

Dans l’application d’échantillon, `SYSTEM_TEXT_JSON` commenter la directive de [préprocesseur](xref:index#preprocessor-directives-in-sample-code) dans *CustomWriterStartup.cs* pour permettre la `Newtonsoft.Json` version de `WriteResponse`.

L’API vérifie la santé ne fournit pas de support intégré pour les formats de retour JSON complexes parce que le format est spécifique à votre choix de système de surveillance. Personnalisez la réponse dans les exemples précédents au besoin. Pour plus d’informations sur `System.Text.Json`la sérialisation JSON avec , voir [Comment sérialiser et déséialiser JSON en .NET](/dotnet/standard/serialization/system-text-json-how-to).

## <a name="database-probe"></a>Sondage de base de données

Un contrôle d’intégrité peut spécifier une requête de base de données à exécuter en tant que test booléen pour indiquer si la base de données répond normalement.

L’exemple d’application utilise [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), une bibliothèque de vérification d’intégrité pour les applications ASP.NET Core, pour effectuer une vérification d’intégrité sur une base de données SQL Server. `AspNetCore.Diagnostics.HealthChecks` exécute une demande `SELECT 1` sur la base de données pour confirmer que la connexion à la base de données est saine.

> [!WARNING]
> Lorsque vous vérifiez une connexion de base de données à l’aide d’une requête, choisissez une requête qui est retournée rapidement. L’utilisation d’une requête risque néanmoins d’entraîner une surcharge de la base de données et d’en diminuer les performances. Dans la plupart des cas, il n’est pas nécessaire d’utiliser une requête de test. Il suffit simplement d’établir une connexion à la base de données. Si vous avez besoin d’exécuter une requête, choisissez une requête SELECT simple, telle que `SELECT 1`.

Inclure une référence de package à [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Fournissez une chaîne de connexion de base de données valide dans le fichier *appsettings.json* de l’exemple d’application. L’application utilise une base de données SQL Server nommée `HealthCheckSample` :

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. L’exemple d’application appelle la méthode `AddSqlServer` avec la chaîne de connexion de la base de données (*DbHealthStartup.cs*) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Un point de terminaison `MapHealthChecks` `Startup.Configure`de bilan de santé est créé en appelant :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Pour exécuter le scénario de sondage d’une base de données à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas maintenu ou pris en charge par Microsoft.

## <a name="entity-framework-core-dbcontext-probe"></a>Sondage DbContext Entity Framework Core

La vérification `DbContext` vérifie que l’application peut communiquer avec la base de données configurée pour un `DbContext` EF Core. La vérification `DbContext` est prise en charge dans les applications qui :

* Utilisent [Entity Framework (EF) Core](/ef/core/).
* Incluent une référence de package à [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` inscrit un contrôle d’intégrité pour un `DbContext`. Le `DbContext` est fourni en tant que `TContext` à la méthode. Une surcharge est disponible pour configurer l’état d’échec, les étiquettes et une requête de test personnalisée.

Par défaut :

* `DbContextHealthCheck` appelle la méthode `CanConnectAsync` d’EF Core. Vous pouvez choisir quelle opération doit être exécutée lors du contrôle d’intégrité à l’aide des surcharges de la méthode `AddDbContextCheck`.
* Le nom du contrôle d’intégrité correspond à celui du type `TContext`.

Dans l’application `AppDbContext` échantillon, `AddDbContextCheck` est fourni et `Startup.ConfigureServices` enregistré comme un service dans (*DbContextHealthStartup.cs*) ) ) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Un point de terminaison `MapHealthChecks` `Startup.Configure`de bilan de santé est créé en appelant :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Pour exécuter le scénario de sondage `DbContext` à l’aide de l’exemple d’application, vérifiez que la base de données spécifiée par le la chaîne de connexion ne se trouve pas déjà dans l’instance SQL Server. Si la base de données existe, supprimez-la.

Exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario dbcontext
```

Une fois que l’application est en cours d’exécution, vérifiez l’état d’intégrité en envoyant une requête au point de terminaison `/health` dans un navigateur. La base de données et `AppDbContext` n’existent pas, donc l’application fournit la réponse suivante :

```
Unhealthy
```

Déclenchez l’exemple d’application pour créer la base de données. Envoyez une requête à `/createdatabase`. L’application répond :

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Envoyez une requête au point de terminaison `/health`. La base de données et le contexte existent, donc l’application répond :

```
Healthy
```

Déclenchez l’exemple d’application pour supprimer la base de données. Envoyez une requête à `/deletedatabase`. L’application répond :

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Envoyez une requête au point de terminaison `/health`. L’application fournit une réponse non intégrité :

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Séparer les sondages probe readiness et probe liveness

Dans certains scénarios d’hébergement, une paire de contrôles d’intégrité est utilisée pour distinguer deux états d’application :

* L’application fonctionne mais n’est pas encore prête à recevoir des requêtes. Il s’agit de l’état de *readiness* qui indique que l’application est prête.
* L’application fonctionne et répond aux requêtes. Il s’agit de l’état de *liveness* qui indique que l’application est active.

En général, la vérification qui permet de savoir si une application est prête implique un ensemble de vérifications plus complet permettant de déterminer si tous les sous-systèmes et toutes les ressources de l’application sont disponibles, ce qui est un processus assez long. La vérification qui permet de savoir si une application est active est simple et rapide, et vise à déterminer si l’application est disponible pour traiter les requêtes. Une fois que l’application est considérée comme prête, il est inutile de recommencer le test. En effet, le seul test nécessaire ensuite est celui visant à vérifier que l’application est active.

L’exemple d’application contient un contrôle d’intégrité permettant de signaler l’achèvement d’une tâche de démarrage de longue durée dans un [service hébergé](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck` expose la propriété `StartupTaskCompleted`, que le service hébergé peut définir sur `true` lorsque sa tâche de longue durée est terminée (*StartupHostedServiceHealthCheck.cs*) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

La tâche de longue durée en arrière-plan est démarrée par un [service hébergé](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*). À la fin de la tâche, `StartupHostedServiceHealthCheck.StartupTaskCompleted` est défini sur `true` :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Le contrôle d’intégrité est inscrit auprès de <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> dans `Startup.ConfigureServices` en même temps que le service hébergé. Étant donné que le service hébergé doit définir la propriété sur le contrôle d’intégrité, le contrôle d’intégrité est également inscrit dans le conteneur du service (*LivenessProbeStartup.cs*) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Un point de terminaison `MapHealthChecks` `Startup.Configure`de bilan de santé est créé en appelant dedans . Dans l’application d’échantillon, les paramètres de bilan de bilan sont créés à :

* `/health/ready`pour le contrôle de préparation. Le test qui permet de vérifier si l’application est prête filtre les contrôles d’intégrité pour n’afficher que celui dont l’étiquette est `ready`.
* `/health/live`pour le contrôle de la vivacité. Le contrôle de la `StartupHostedServiceHealthCheck` vivacité `false` filtre le en revenant dans le [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (pour plus d’informations, voir [Les contrôles de santé Filtre](#filter-health-checks))

Dans l’exemple suivant, code :

* Le chèque de préparation utilise tous les chèques enregistrés avec l’étiquette «prêt».
* L’exclut `Predicate` tous les contrôles et renvoie un 200-Ok.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

Pour exécuter le scénario de configuration readiness/liveness à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario liveness
```

Dans un navigateur, accédez à `/health/ready` plusieurs fois jusqu’à ce que 15 secondes soient écoulées. Le contrôle d’intégrité signale *Unhealthy* pendant les 15 premières secondes. Après 15 secondes, le point de terminaison signale *Healthy*, ce qui reflète l’achèvement de la tâche de longue durée exécutée par le service hébergé.

Cet exemple crée également un éditeur de vérification de l’intégrité (implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) qui exécute la première vérification de disponibilité avec un délai de deux secondes. Pour plus d’informations, consultez la section [Éditeur de vérification de l’intégrité](#health-check-publisher).

### <a name="kubernetes-example"></a>Exemple Kubernetes

Dans un environnement tel que [Kubernetes](https://kubernetes.io/), il peut être utile d’utiliser séparément le test permettant de savoir si la l’application est prête et celui visant à savoir si l’application est active. Dans Kubernetes, une application peut devoir effectuer une tâche de démarrage de longue durée avant d’accepter des requêtes, telles qu’un test de la disponibilité de la base de données sous-jacente. L’utilisation séparée des deux tests permet à l’orchestrateur de faire la distinction entre une application qui fonctionne mais qui n’est pas encore prête, et une application qui n’a pas pu démarrer. Pour plus d’informations sur les sondages probe readiness et probe liveness dans Kubernetes, consultez [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) dans la documentation Kubernetes.

L’exemple suivant montre une configuration probe readiness Kubernetes :

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Sonde basée sur les métriques avec un enregistreur de réponse personnalisé

L’exemple d’application montre un contrôle d’intégrité de mémoire avec un enregistreur de réponse personnalisé.

`MemoryHealthCheck` signale un état détérioré si l’application utilise plus de mémoire que le seuil défini (1 Go dans l’exemple d’application). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> inclut des informations de récupérateur de mémoire pour l’application (*MemoryHealthCheck.cs*) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. Au lieu d’activer le contrôle d’intégrité en le passant à <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` est inscrit en tant que service. Tous les services <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> inscrits sont disponibles pour les services de contrôle d’intégrité et le middleware. Nous vous recommandons d’inscrire les services de contrôle d’intégrité en tant que services Singleton.

Dans *CustomWriterStartup.cs* de l’application d’échantillon :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Un point de terminaison `MapHealthChecks` `Startup.Configure`de bilan de santé est créé en appelant dedans . Un `WriteResponse` délégué est fourni à la <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> propriété à la sortie d’une réponse personnalisée JSON lorsque le bilan de santé exécute:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

Le `WriteResponse` délégué formate l’objet `CompositeHealthCheckResult` JSON et donne la sortie JSON pour la réponse de bilan de santé. Pour plus d’informations, consultez la section [De sortie Customize.](#customize-output)

Pour exécuter le sondage basé sur les métriques avec l’enregistreur de réponse personnalisé à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclut des scénarios de vérification de l’intégrité basés sur des métriques, notamment des vérifications du stockage sur disque et de la vivacité maximale des valeurs.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas maintenu ou pris en charge par Microsoft.

## <a name="filter-by-port"></a>Filtrer par port

Faites `RequireHost` `MapHealthChecks` appel à un modèle d’URL qui spécifie un port pour limiter les demandes de bilan de santé au port spécifié. Ceci est généralement utilisé dans les environnements de conteneurs pour exposer un port aux services de supervision.

L’exemple d’application configure le port à l’aide du [fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Le port est défini dans le fichier *launchSettings.json*, puis transmis au fournisseur de configuration via une variable d’environnement. Vous devez également configurer le serveur pour écouter les requêtes qui arrivent au port de gestion.

Pour utiliser l’exemple d’application dans le but d’illustrer la configuration du port de gestion, créez le fichier *launchSettings.json* dans un dossier *Propriétés*.

Le fichier *Properties/launchSettings.json* suivant dans l’application de l’échantillon n’est pas inclus dans les fichiers de projet de l’application de l’échantillon et doit être créé manuellement :

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. Créez un point de `MapHealthChecks` terminaison de bilan de santé en appelant dedans `Startup.Configure`.

Dans l’application d’échantillon, un appel à `RequireHost` sur le point de terminaison précise `Startup.Configure` le port de gestion de configuration :

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

Les points de terminaison `Startup.Configure`sont créés dans l’application d’échantillon dans . Dans l’exemple suivant, code :

* Le chèque de préparation utilise tous les chèques enregistrés avec l’étiquette «prêt».
* L’exclut `Predicate` tous les contrôles et renvoie un 200-Ok.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> Vous pouvez éviter de créer le fichier *launchSettings.json* dans l’application d’échantillon en définissant explicitement le port de gestion dans le code. Dans *Program.cs* où <xref:Microsoft.Extensions.Hosting.HostBuilder> l’est créé, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> ajoutez un appel et fournissez le point de terminaison du port de gestion de l’application. Dans `Configure` *ManagementPortStartup.cs*, spécifier le `RequireHost`port de gestion avec :
>
> *Program.cs*:
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> *ManagementPortStartup.cs* :
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

Pour exécuter le scénario de configuration du port de gestion à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuer une bibliothèque de contrôle d’intégrité

Pour distribuer une bibliothèque comme un contrôle d’intégrité :

1. Écrivez un contrôle d’intégrité qui implémente l’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> comme une classe autonome. La classe peut utiliser une [injection de dépendance](xref:fundamentals/dependency-injection), une activation de type et des [options nommées](xref:fundamentals/configuration/options) pour accéder aux données de configuration.

   Dans la logique `CheckHealthAsync`des contrôles de santé de :

   * `data1`et `data2` sont utilisés dans la méthode pour exécuter la logique de bilan de santé de la sonde.
   * `AccessViolationException`est manipulé.

   Lorsqu’un produit, <xref:System.AccessViolationException> le <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> est retourné avec le pour permettre aux utilisateurs de configurer l’état de défaillance des contrôles de santé.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. Écrivez une méthode d’extension avec des paramètres que l’application consommatrice appelle dans sa méthode `Startup.Configure`. Dans l’exemple qui suit, prenons la signature de méthode de contrôle d’intégrité suivante :

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   La signature précédente indique que `ExampleHealthCheck` nécessite des données supplémentaires pour traiter la logique de sondage du contrôle d’intégrité. Les données sont fournies au délégué qui est utilisé pour créer l’instance de contrôle d’intégrité lorsque le contrôle d’intégrité est inscrit avec une méthode d’extension. Dans l’exemple qui suit, l’appelant spécifie les éléments facultatifs suivants :

   * Nom du contrôle d’intégrité (`name`). Si `null`, `example_health_check` est utilisé.
   * Point de données de chaîne du contrôle d’intégrité (`data1`).
   * Point de données Integer du contrôle d’intégrité (`data2`). Si `null`, `1` est utilisé.
   * État d’échec (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Par défaut, il s’agit de `null`. Si `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) est signalé pour un état d’échec.
   * Étiquettes (`IEnumerable<string>`).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Serveur de publication des contrôles d’intégrité

Quand un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> est ajouté au conteneur du service, le système de contrôle d’intégrité exécute régulièrement vos contrôles d’intégrité et appelle `PublishAsync` avec le résultat. C’est utile dans un scénario impliquant un système de supervision basé sur les envois (push) et nécessitant que chaque processus appelle le système de supervision régulièrement afin de déterminer l’état d’intégrité.

L’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> comprend une seule méthode :

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> vous autorise à définir :

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Le délai initial s’est appliqué <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> après le début de l’application avant l’exécution des instances. Le retard est appliqué à une seule fois au démarrage et ne s’applique pas aux itérations ultérieures. La valeur par défaut est de cinq secondes.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; La période <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> d’exécution. La valeur par défaut est de 30 secondes.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; En <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` cas de (défaut), le service d’éditeur de bilans de santé exécute tous les contrôles de santé enregistrés. Pour exécuter un sous-ensemble de contrôles d’intégrité, fournissez une fonction qui filtre l’ensemble de vérifications. Le prédicat est évalué à chaque période.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Le délai d’exécution des contrôles <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> de santé pour tous les cas. Utilisez <xref:System.Threading.Timeout.InfiniteTimeSpan> pour une exécution sans délai d’attente. La valeur par défaut est de 30 secondes.

Dans l’exemple d’application, `ReadinessPublisher` est une implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. L’état de bilan est enregistré pour chaque vérification à un niveau de journal de:

* Informations<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ) si l’état de santé est <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.
* Erreur<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ) si <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> le <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>statut est soit ou .

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

Dans l’exemple d’application `LivenessProbeStartup`, la vérification de la disponibilité `StartupHostedService` a un délai de démarrage de deux secondes et exécute la vérification toutes les 30 secondes. Pour activer l’implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, l’exemple enregistre `ReadinessPublisher` comme un service singleton dans le conteneur [d’injection de dépendance (DI)](xref:fundamentals/dependency-injection) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclut les serveurs de publication pour plusieurs systèmes, notamment [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas maintenu ou pris en charge par Microsoft.

## <a name="restrict-health-checks-with-mapwhen"></a>Restreindre les vérifications d’intégrité avec MapWhen

Utilisez <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> pour créer une branche conditionnelle du pipeline des demandes pour les points de terminaison de contrôle d’intégrité.

Dans l’exemple `MapWhen` suivant, branche le pipeline de demande pour activer `api/HealthCheck` Health Checks Middleware si une demande GET est reçue pour le point de terminaison :

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

Pour plus d’informations, consultez <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core offre des contrôles de santé Middleware et des bibliothèques pour signaler la santé des composants de l’infrastructure des applications.

Les contrôles d’intégrité sont exposés par une application comme des points de terminaison HTTP. Les points de terminaison de vérification d’intégrité peuvent être configurés pour de nombreux scénarios de supervision en temps réel :

* Les sondes d’intégrité peuvent être utilisées par les orchestrateurs de conteneurs et les équilibreurs de charge afin de vérifier l’état d’une application. Par exemple, un orchestrateur de conteneurs peut répondre à un résultat de non intégrité en arrêtant le déploiement ou en redémarrant un conteneur. Face à une application non saine, l’équilibreur de charge peut réagir en redirigeant le trafic vers une instance saine.
* L’utilisation de la mémoire, des disques et des autres ressources de serveur physique peut être supervisée dans le cadre d’un contrôle d’intégrité.
* Les contrôles d’intégrité peuvent tester les dépendances d’une application, telles que les bases de données et les points de terminaison de service externes, dans le but de vérifier leur disponibilité et leur bon fonctionnement.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

L’exemple d’application comprend des exemples pour les scénarios décrits dans cette rubrique. Pour exécuter l’exemple d’application selon un scénario donné, utilisez la commande [dotnet run](/dotnet/core/tools/dotnet-run) dans un interpréteur de commandes, à partir du dossier du projet. Pour plus d’informations sur l’utilisation de l’exemple d’application, consultez le fichier *README.md* de l’exemple d’application ainsi que les descriptions de scénarios de cette rubrique.

## <a name="prerequisites"></a>Prérequis

Les contrôles d’intégrité sont généralement utilisés avec un service de supervision externe ou un orchestrateur de conteneurs pour vérifier l’état d’une application. Avant d’ajouter des contrôles d’intégrité à une application, vous devez décider du système de supervision à utiliser. Le système de supervision détermine les types de contrôles d’intégrité qui doivent être créés ainsi que la configuration de leurs points de terminaison.

Référencez le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou ajoutez une référence de package au package [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks).

L’exemple d’application fournit du code de démarrage pour illustrer les contrôles d’intégrité de plusieurs scénarios. Le scénario [probe de la base de données](#database-probe) vérifie l’intégrité d’une connexion de base de données à l’aide d’[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Le scénario [Sondage DbContext](#entity-framework-core-dbcontext-probe) vérifie une base de données à l’aide d’un `DbContext` EF Core. Pour explorer les scénarios de base de données, l’exemple d’application :

* Crée une base de données et fournit sa chaîne de connexion dans le fichier *appsettings.json*.
* Possède les références de package suivantes dans son fichier de projet :
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas maintenu ou pris en charge par Microsoft.

Un autre scénario de contrôle d’intégrité montre comment filtrer des contrôles d’intégrité selon un port de gestion. L’exemple d’application vous oblige à créer un fichier *Properties/launchSettings.json* comprenant l’URL de gestion et le port de gestion. Pour plus d’informations, consultez la section [Filtrer par port](#filter-by-port).

## <a name="basic-health-probe"></a>Sondage d’intégrité de base

Pour de nombreuses applications, un sondage d’intégrité de base qui signale la disponibilité d’une application pour le traitement des requêtes (*liveness*) suffit à découvrir l’état de l’application.

La configuration de base enregistre les services de bilan de santé et appelle le Health Checks Middleware pour répondre à un point de terminaison URL avec une réponse sanitaire. Par défaut, aucun contrôle d’intégrité n’est inscrit pour tester les dépendances ou le sous-système. L’application est considérée comme saine si elle est capable de répondre à l’URL de point de terminaison de contrôle d’intégrité. L’enregistreur de réponse par défaut écrit l’état (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) sous forme de texte en clair qu’il renvoie au client, indiquant si l’état est [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) ou [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. Ajouter un point de terminaison pour les contrôles de santé Middleware avec <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> dans le pipeline de traitement de la demande de `Startup.Configure`.

Dans l’exemple d’application, le point de terminaison de contrôle d’intégrité est créé au niveau de `/health` (*BasicStartup.cs*) :

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

Pour exécuter le scénario de configuration de base à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Exemple Docker

[Docker](xref:host-and-deploy/docker/index) fournit une directive `HEALTHCHECK` intégrée qui peut être utilisée pour vérifier l’état d’une application utilisant la configuration de contrôle d’intégrité de base :

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Créer des contrôles d’intégrité

Les contrôles d’intégrité sont créés via l’implémentation de l’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>. La méthode <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> retourne un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> qui indique l’état d’intégrité comme étant `Healthy`, `Degraded` ou `Unhealthy`. Le résultat est écrit sous la forme de texte en clair avec un code d’état configurable (la configuration est décrite dans la section [Options de contrôle d’intégrité](#health-check-options)). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> peut également retourner des paires clé-valeur facultatives.

### <a name="example-health-check"></a>Exemple de contrôle d’intégrité

La `ExampleHealthCheck` classe suivante démontre la disposition d’un bilan de santé. La logique des contrôles `CheckHealthAsync` de santé est placée dans la méthode. L’exemple suivant définit une `healthCheckResultHealthy`variable `true`factice, , à . Si la `healthCheckResultHealthy` valeur de `false`est réglé à , le statut [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) est retourné.

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a>Inscrire les services de contrôle d’intégrité

Le `ExampleHealthCheck` type est ajouté aux `Startup.ConfigureServices` <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>services de bilan de santé avec :

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

La surcharge <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> de l’exemple suivant définit l’état d’échec (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) de manière à être signalé lorsque le contrôle d’intégrité signale un échec. Si l’état d’échec est défini sur `null` (par défaut), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) est signalé. Cette surcharge est utile pour les créateurs de bibliothèque, dans les cas où l’état d’échec indiqué par la bibliothèque est appliqué par l’application lorsqu’un échec est signalé par le contrôle d’intégrité, si l’implémentation de ce dernier respecte le paramètre.

Des *étiquettes* peuvent être utilisées pour filtrer les contrôles d’intégrité (cette procédure est décrite plus loin dans la section [Filtrer les contrôles d’intégrité](#filter-health-checks)).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> peut également exécuter une fonction lambda. Dans l’exemple suivant, `Startup.ConfigureServices` le nom `Example` du bilan de santé est spécifié au fur et à mesure que le chèque renvoie toujours un état sain :

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a>Utiliser Health Checks Middleware

Dans `Startup.Configure`, appelez <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> dans le pipeline de traitement avec l’URL de point de terminaison ou le chemin relatif :

```csharp
app.UseHealthChecks("/health");
```

Si les contrôles d’intégrité doivent écouter un port en particulier, utilisez une surcharge de <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> pour définir le port (cette procédure est décrite plus loin dans la section [Filtrer par port](#filter-by-port)) :

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a>Options de contrôle d’intégrité

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> permet de personnaliser le comportement du contrôle d’intégrité :

* [Filtrer les contrôles d’intégrité](#filter-health-checks)
* [Personnaliser le code d’état HTTP](#customize-the-http-status-code)
* [Supprimer les en-têtes de cache](#suppress-cache-headers)
* [Personnaliser la sortie](#customize-output)

### <a name="filter-health-checks"></a>Filtrer les contrôles d’intégrité

Par défaut, Health Checks Middleware exécute tous les contrôles de santé enregistrés. Pour exécuter un sous-ensemble de contrôles d’intégrité, fournissez une fonction qui retourne une valeur booléenne à l’option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>. Dans l’exemple suivant, le contrôle d’intégrité `Bar` est filtré en fonction de son étiquette (`bar_tag`) dans l’instruction conditionnelle de la fonction, où `true` est retourné uniquement si la propriété <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> du contrôle d’intégrité correspond à `foo_tag` ou à `baz_tag` :

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a>Personnaliser le code d’état HTTP

Utilisez <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> pour personnaliser le mappage de l’état d’intégrité aux codes d’état HTTP. Les affectations <xref:Microsoft.AspNetCore.Http.StatusCodes> suivantes sont les valeurs par défaut utilisées par le middleware. Vous pouvez modifier les valeurs de code d’état selon vos besoins.

Dans `Startup.Configure` :

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a>Supprimer les en-têtes de cache

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>contrôle si le Health Checks Middleware ajoute des en-têtes HTTP à une réponse de sonde pour empêcher la mise en cache de réponse. Si la valeur est `false` (par défaut), le middleware définit ou substitue les en-têtes `Cache-Control`, `Expires` et `Pragma` afin d’empêcher la mise en cache de la réponse. Si la valeur est `true`, le middleware ne modifie pas les en-têtes de cache de la réponse.

Dans `Startup.Configure` :

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a>Personnaliser la sortie

L’option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> obtient ou définit un délégué utilisé pour écrire la réponse. Le délégué par défaut écrit une réponse minimale constituée de texte en clair, avec la valeur de chaîne [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).

Dans `Startup.Configure` :

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

Le délégué par défaut écrit une réponse minimale constituée de texte en clair, avec la valeur de chaîne [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Le délégué personnalisé `WriteResponse`suivant, , produit une réponse personnalisée JSON:

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

Le système de vérifications de santé ne fournit pas de support intégré pour les formats de retour JSON complexes parce que le format est spécifique à votre choix de système de surveillance. N’hésitez pas `JObject` à personnaliser l’exemple précédent au besoin pour répondre à vos besoins.

## <a name="database-probe"></a>Sondage de base de données

Un contrôle d’intégrité peut spécifier une requête de base de données à exécuter en tant que test booléen pour indiquer si la base de données répond normalement.

L’exemple d’application utilise [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), une bibliothèque de vérification d’intégrité pour les applications ASP.NET Core, pour effectuer une vérification d’intégrité sur une base de données SQL Server. `AspNetCore.Diagnostics.HealthChecks` exécute une demande `SELECT 1` sur la base de données pour confirmer que la connexion à la base de données est saine.

> [!WARNING]
> Lorsque vous vérifiez une connexion de base de données à l’aide d’une requête, choisissez une requête qui est retournée rapidement. L’utilisation d’une requête risque néanmoins d’entraîner une surcharge de la base de données et d’en diminuer les performances. Dans la plupart des cas, il n’est pas nécessaire d’utiliser une requête de test. Il suffit simplement d’établir une connexion à la base de données. Si vous avez besoin d’exécuter une requête, choisissez une requête SELECT simple, telle que `SELECT 1`.

Inclure une référence de package à [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Fournissez une chaîne de connexion de base de données valide dans le fichier *appsettings.json* de l’exemple d’application. L’application utilise une base de données SQL Server nommée `HealthCheckSample` :

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. L’exemple d’application appelle la méthode `AddSqlServer` avec la chaîne de connexion de la base de données (*DbHealthStartup.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Appelez Health Checks Middleware dans `Startup.Configure`le pipeline de traitement de l’application dans :

```csharp
app.UseHealthChecks("/health");
```

Pour exécuter le scénario de sondage d’une base de données à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas maintenu ou pris en charge par Microsoft.

## <a name="entity-framework-core-dbcontext-probe"></a>Sondage DbContext Entity Framework Core

La vérification `DbContext` vérifie que l’application peut communiquer avec la base de données configurée pour un `DbContext` EF Core. La vérification `DbContext` est prise en charge dans les applications qui :

* Utilisent [Entity Framework (EF) Core](/ef/core/).
* Incluent une référence de package à [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` inscrit un contrôle d’intégrité pour un `DbContext`. Le `DbContext` est fourni en tant que `TContext` à la méthode. Une surcharge est disponible pour configurer l’état d’échec, les étiquettes et une requête de test personnalisée.

Par défaut :

* `DbContextHealthCheck` appelle la méthode `CanConnectAsync` d’EF Core. Vous pouvez choisir quelle opération doit être exécutée lors du contrôle d’intégrité à l’aide des surcharges de la méthode `AddDbContextCheck`.
* Le nom du contrôle d’intégrité correspond à celui du type `TContext`.

Dans l’application `AppDbContext` échantillon, `AddDbContextCheck` est fourni et `Startup.ConfigureServices` enregistré comme un service dans (*DbContextHealthStartup.cs*) ) ) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Dans l’application `UseHealthChecks` d’échantillon, ajoute `Startup.Configure`le Middleware de contrôles de santé dans .

```csharp
app.UseHealthChecks("/health");
```

Pour exécuter le scénario de sondage `DbContext` à l’aide de l’exemple d’application, vérifiez que la base de données spécifiée par le la chaîne de connexion ne se trouve pas déjà dans l’instance SQL Server. Si la base de données existe, supprimez-la.

Exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario dbcontext
```

Une fois que l’application est en cours d’exécution, vérifiez l’état d’intégrité en envoyant une requête au point de terminaison `/health` dans un navigateur. La base de données et `AppDbContext` n’existent pas, donc l’application fournit la réponse suivante :

```
Unhealthy
```

Déclenchez l’exemple d’application pour créer la base de données. Envoyez une requête à `/createdatabase`. L’application répond :

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Envoyez une requête au point de terminaison `/health`. La base de données et le contexte existent, donc l’application répond :

```
Healthy
```

Déclenchez l’exemple d’application pour supprimer la base de données. Envoyez une requête à `/deletedatabase`. L’application répond :

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Envoyez une requête au point de terminaison `/health`. L’application fournit une réponse non intégrité :

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Séparer les sondages probe readiness et probe liveness

Dans certains scénarios d’hébergement, une paire de contrôles d’intégrité est utilisée pour distinguer deux états d’application :

* L’application fonctionne mais n’est pas encore prête à recevoir des requêtes. Il s’agit de l’état de *readiness* qui indique que l’application est prête.
* L’application fonctionne et répond aux requêtes. Il s’agit de l’état de *liveness* qui indique que l’application est active.

En général, la vérification qui permet de savoir si une application est prête implique un ensemble de vérifications plus complet permettant de déterminer si tous les sous-systèmes et toutes les ressources de l’application sont disponibles, ce qui est un processus assez long. La vérification qui permet de savoir si une application est active est simple et rapide, et vise à déterminer si l’application est disponible pour traiter les requêtes. Une fois que l’application est considérée comme prête, il est inutile de recommencer le test. En effet, le seul test nécessaire ensuite est celui visant à vérifier que l’application est active.

L’exemple d’application contient un contrôle d’intégrité permettant de signaler l’achèvement d’une tâche de démarrage de longue durée dans un [service hébergé](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck` expose la propriété `StartupTaskCompleted`, que le service hébergé peut définir sur `true` lorsque sa tâche de longue durée est terminée (*StartupHostedServiceHealthCheck.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

La tâche de longue durée en arrière-plan est démarrée par un [service hébergé](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*). À la fin de la tâche, `StartupHostedServiceHealthCheck.StartupTaskCompleted` est défini sur `true` :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Le contrôle d’intégrité est inscrit auprès de <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> dans `Startup.ConfigureServices` en même temps que le service hébergé. Étant donné que le service hébergé doit définir la propriété sur le contrôle d’intégrité, le contrôle d’intégrité est également inscrit dans le conteneur du service (*LivenessProbeStartup.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Appelez Health Checks Middleware dans `Startup.Configure`le pipeline de traitement de l’application dans . Dans l’exemple d’application, les points de terminaison de contrôle d’intégrité sont créés au niveau de `/health/ready` pour vérifier si l’application est prête, et au niveau de `/health/live` pour vérifier si l’application est active. Le test qui permet de vérifier si l’application est prête filtre les contrôles d’intégrité pour n’afficher que celui dont l’étiquette est `ready`. Le test qui permet de vérifier si l’application est active exclut le `StartupHostedServiceHealthCheck` en retournant `false` dans [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (pour plus d’informations, consultez [Filtrer les contrôles d’intégrité](#filter-health-checks)) :

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

Pour exécuter le scénario de configuration readiness/liveness à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario liveness
```

Dans un navigateur, accédez à `/health/ready` plusieurs fois jusqu’à ce que 15 secondes soient écoulées. Le contrôle d’intégrité signale *Unhealthy* pendant les 15 premières secondes. Après 15 secondes, le point de terminaison signale *Healthy*, ce qui reflète l’achèvement de la tâche de longue durée exécutée par le service hébergé.

Cet exemple crée également un éditeur de vérification de l’intégrité (implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) qui exécute la première vérification de disponibilité avec un délai de deux secondes. Pour plus d’informations, consultez la section [Éditeur de vérification de l’intégrité](#health-check-publisher).

### <a name="kubernetes-example"></a>Exemple Kubernetes

Dans un environnement tel que [Kubernetes](https://kubernetes.io/), il peut être utile d’utiliser séparément le test permettant de savoir si la l’application est prête et celui visant à savoir si l’application est active. Dans Kubernetes, une application peut devoir effectuer une tâche de démarrage de longue durée avant d’accepter des requêtes, telles qu’un test de la disponibilité de la base de données sous-jacente. L’utilisation séparée des deux tests permet à l’orchestrateur de faire la distinction entre une application qui fonctionne mais qui n’est pas encore prête, et une application qui n’a pas pu démarrer. Pour plus d’informations sur les sondages probe readiness et probe liveness dans Kubernetes, consultez [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) dans la documentation Kubernetes.

L’exemple suivant montre une configuration probe readiness Kubernetes :

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Sonde basée sur les métriques avec un enregistreur de réponse personnalisé

L’exemple d’application montre un contrôle d’intégrité de mémoire avec un enregistreur de réponse personnalisé.

`MemoryHealthCheck`signale un statut malsain si l’application utilise plus d’un seuil de mémoire donné (1 Go dans l’application de l’échantillon). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> inclut des informations de récupérateur de mémoire pour l’application (*MemoryHealthCheck.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. Au lieu d’activer le contrôle d’intégrité en le passant à <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` est inscrit en tant que service. Tous les services <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> inscrits sont disponibles pour les services de contrôle d’intégrité et le middleware. Nous vous recommandons d’inscrire les services de contrôle d’intégrité en tant que services Singleton.

Dans l’application d’échantillon *(CustomWriterStartup.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Appelez Health Checks Middleware dans `Startup.Configure`le pipeline de traitement de l’application dans . Un délégué `WriteResponse` est fourni à la propriété `ResponseWriter` pour produire une réponse JSON personnalisée lorsque le contrôle d’intégrité est exécuté :

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

La méthode `WriteResponse` formate le `CompositeHealthCheckResult` en un objet JSON et génère une sortie JSON pour la réponse du contrôle d’intégrité :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Pour exécuter le sondage basé sur les métriques avec l’enregistreur de réponse personnalisé à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclut des scénarios de vérification de l’intégrité basés sur des métriques, notamment des vérifications du stockage sur disque et de la vivacité maximale des valeurs.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas maintenu ou pris en charge par Microsoft.

## <a name="filter-by-port"></a>Filtrer par port

Si vous appelez <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> avec un port, les requêtes de contrôle d’intégrité seront limitées au port spécifié. Ceci est généralement utilisé dans les environnements de conteneurs pour exposer un port aux services de supervision.

L’exemple d’application configure le port à l’aide du [fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Le port est défini dans le fichier *launchSettings.json*, puis transmis au fournisseur de configuration via une variable d’environnement. Vous devez également configurer le serveur pour écouter les requêtes qui arrivent au port de gestion.

Pour utiliser l’exemple d’application dans le but d’illustrer la configuration du port de gestion, créez le fichier *launchSettings.json* dans un dossier *Propriétés*.

Le fichier *Properties/launchSettings.json* suivant dans l’application de l’échantillon n’est pas inclus dans les fichiers de projet de l’application de l’échantillon et doit être créé manuellement :

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Pour inscrire les services de contrôle d’intégrité, utilisez <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> dans `Startup.ConfigureServices`. L’appel à <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> spécifie le port de gestion (*ManagementPortStartup.cs*) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> Vous pouvez éviter de créer le fichier *launchSettings.json* dans l’exemple d’application en définissant explicitement les URL et le port de gestion dans le code. Dans *Program.cs* où <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> est créé, ajoutez un appel à <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> et fournissez le point de terminaison de réponse normal et le point de terminaison de port de gestion de l’application. Dans *ManagementPortStartup.cs* où <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> est appelé, spécifiez le port de gestion explicitement.
>
> *Program.cs*:
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> *ManagementPortStartup.cs* :
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

Pour exécuter le scénario de configuration du port de gestion à l’aide de l’exemple d’application, exécutez la commande suivante à partir du dossier du projet, dans un interpréteur de commandes :

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuer une bibliothèque de contrôle d’intégrité

Pour distribuer une bibliothèque comme un contrôle d’intégrité :

1. Écrivez un contrôle d’intégrité qui implémente l’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> comme une classe autonome. La classe peut utiliser une [injection de dépendance](xref:fundamentals/dependency-injection), une activation de type et des [options nommées](xref:fundamentals/configuration/options) pour accéder aux données de configuration.

   Dans la logique `CheckHealthAsync`des contrôles de santé de :

   * `data1`et `data2` sont utilisés dans la méthode pour exécuter la logique de bilan de santé de la sonde.
   * `AccessViolationException`est manipulé.

   Lorsqu’un produit, <xref:System.AccessViolationException> le <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> est retourné avec le pour permettre aux utilisateurs de configurer l’état de défaillance des contrôles de santé.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. Écrivez une méthode d’extension avec des paramètres que l’application consommatrice appelle dans sa méthode `Startup.Configure`. Dans l’exemple qui suit, prenons la signature de méthode de contrôle d’intégrité suivante :

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   La signature précédente indique que `ExampleHealthCheck` nécessite des données supplémentaires pour traiter la logique de sondage du contrôle d’intégrité. Les données sont fournies au délégué qui est utilisé pour créer l’instance de contrôle d’intégrité lorsque le contrôle d’intégrité est inscrit avec une méthode d’extension. Dans l’exemple qui suit, l’appelant spécifie les éléments facultatifs suivants :

   * Nom du contrôle d’intégrité (`name`). Si `null`, `example_health_check` est utilisé.
   * Point de données de chaîne du contrôle d’intégrité (`data1`).
   * Point de données Integer du contrôle d’intégrité (`data2`). Si `null`, `1` est utilisé.
   * État d’échec (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Par défaut, il s’agit de `null`. Si `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) est signalé pour un état d’échec.
   * Étiquettes (`IEnumerable<string>`).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Serveur de publication des contrôles d’intégrité

Quand un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> est ajouté au conteneur du service, le système de contrôle d’intégrité exécute régulièrement vos contrôles d’intégrité et appelle `PublishAsync` avec le résultat. C’est utile dans un scénario impliquant un système de supervision basé sur les envois (push) et nécessitant que chaque processus appelle le système de supervision régulièrement afin de déterminer l’état d’intégrité.

L’interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> comprend une seule méthode :

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> vous autorise à définir :

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Le délai initial s’est appliqué <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> après le début de l’application avant l’exécution des instances. Le retard est appliqué à une seule fois au démarrage et ne s’applique pas aux itérations ultérieures. La valeur par défaut est de cinq secondes.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; La période <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> d’exécution. La valeur par défaut est de 30 secondes.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; En <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` cas de (défaut), le service d’éditeur de bilans de santé exécute tous les contrôles de santé enregistrés. Pour exécuter un sous-ensemble de contrôles d’intégrité, fournissez une fonction qui filtre l’ensemble de vérifications. Le prédicat est évalué à chaque période.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Le délai d’exécution des contrôles <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> de santé pour tous les cas. Utilisez <xref:System.Threading.Timeout.InfiniteTimeSpan> pour une exécution sans délai d’attente. La valeur par défaut est de 30 secondes.

> [!WARNING]
> Dans la version ASP.NET Core 2.2, le paramètre <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> n’est pas respecté par l’implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ; il définit la valeur de <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>. Cette question a été abordée dans ASP.NET Core 3.0.

Dans l’exemple d’application, `ReadinessPublisher` est une implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. L’état de bilan est enregistré pour chaque vérification comme l’un ou l’autre:

* Informations<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ) si l’état de santé est <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.
* Erreur<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ) si <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> le <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>statut est soit ou .

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

Dans l’exemple d’application `LivenessProbeStartup`, la vérification de la disponibilité `StartupHostedService` a un délai de démarrage de deux secondes et exécute la vérification toutes les 30 secondes. Pour activer l’implémentation <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, l’exemple enregistre `ReadinessPublisher` comme un service singleton dans le conteneur [d’injection de dépendance (DI)](xref:fundamentals/dependency-injection) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> La solution de contournement suivante autorise l’ajout d’une instance <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> au conteneur de service lorsqu’un ou plusieurs autres services hébergés ont déjà été ajoutés à l’application. Cette solution de contournement ne sera pas nécessaire dans ASP.NET Core 3.0.
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclut les serveurs de publication pour plusieurs systèmes, notamment [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) n’est pas maintenu ou pris en charge par Microsoft.

## <a name="restrict-health-checks-with-mapwhen"></a>Restreindre les vérifications d’intégrité avec MapWhen

Utilisez <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> pour créer une branche conditionnelle du pipeline des demandes pour les points de terminaison de contrôle d’intégrité.

Dans l’exemple `MapWhen` suivant, branche le pipeline de demande pour activer `api/HealthCheck` Health Checks Middleware si une demande GET est reçue pour le point de terminaison :

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

Pour plus d’informations, consultez <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end
