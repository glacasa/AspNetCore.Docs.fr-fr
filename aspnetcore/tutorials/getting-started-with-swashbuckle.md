---
title: Bien démarrer avec Swashbuckle et ASP.NET Core
author: zuckerthoben
description: Découvrez comment ajouter Swashbuckle à votre projet d’API web ASP.NET Core pour intégrer l’IU Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 6e4d80afa1c38344321ad45031ff21fec71ae0a4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776719"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Bien démarrer avec Swashbuckle et ASP.NET Core

De [Shayne Boyer](https://twitter.com/spboyer) et [Scott Addie](https://twitter.com/Scott_Addie)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Swashbuckle compte trois composants principaux :

* [Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/) : modèle objet Swagger et intergiciel (middleware) pour exposer des objets `SwaggerDocument` sous forme de points de terminaison JSON.

* [Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/) : générateur Swagger qui crée des objets `SwaggerDocument` directement à partir de vos routes, contrôleurs et modèles. Il est généralement associé à l’intergiciel de point de terminaison Swagger pour exposer automatiquement Swagger JSON.

* [Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): version incorporée de l’outil IU Swagger. Elle interprète Swagger JSON afin de générer une expérience complète et personnalisable pour décrire la fonctionnalité de l’API web. Il inclut des ateliers de test intégrés pour les méthodes publiques.

## <a name="package-installation"></a>Installation de package

Vous pouvez ajouter Swashbuckle en adoptant l’une des approches suivantes :

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* À partir de la fenêtre **Console du Gestionnaire de package** :
  * Accéder à **la** > console du**Gestionnaire de package** **Windows** > 
  * Accédez au répertoire où se trouve le fichier *TodoApi.csproj*.
  * Exécutez la commande suivante :

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0
    ```

* À partir de la boîte de dialogue **Gérer les packages NuGet** :
  * Cliquez avec le bouton droit sur le projet dans **Explorateur de solutions** > **gérer les packages NuGet**
  * Affectez la valeur « nuget.org » à **Source du package**.
  * Vérifiez que l’option « Inclure la version préliminaire » est activée
  * Entrez « Swashbuckle.AspNetCore » dans la zone de recherche.
  * Sélectionnez le package « Swashbuckle.AspNetCore » le plus récent sous l’onglet **Parcourir** et cliquez sur **Installer**.

### <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Cliquez avec le bouton droit sur le dossier *Packages* dans **Panneau Solutions** > **Ajouter des packages**.
* Dans la fenêtre **Ajouter des packages**, sélectionnez « nuget.org » dans la liste déroulante **Source**.
* Vérifiez que l’option « Afficher les packages en version préliminaire » est activée.
* Entrez « Swashbuckle.AspNetCore » dans la zone de recherche.
* Sélectionnez le package « Swashbuckle.AspNetCore » le plus récent dans le volet de résultats, puis cliquez sur **Ajouter un package**.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez la commande suivante à partir du **Terminal intégré** :

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Exécutez la commande suivante :

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

---

## <a name="add-and-configure-swagger-middleware"></a>Ajouter et configurer l’intergiciel (middleware) Swagger

Dans la classe `Startup`, importez l’espace de noms suivant pour utiliser la classe `OpenApiInfo` :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

Ajoutez le générateur Swagger à la collection de services dans la méthode `Startup.ConfigureServices` :

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

Dans la méthode `Startup.Configure`, activez l’intergiciel pour traiter le document JSON généré et l’IU Swagger :

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

L’appel de méthode `UseSwaggerUI` précédent active le [middleware (intergiciel) des fichiers statiques](xref:fundamentals/static-files). Si vous ciblez .NET Framework ou .NET Core 1. x, ajoutez le package NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet.

Lancez l’application et accédez à `http://localhost:<port>/swagger/v1/swagger.json`. Le document généré décrivant les points de terminaison s’affiche comme illustré dans la [spécification Swagger (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).

L’interface utilisateur Swagger se trouve à l’adresse `http://localhost:<port>/swagger`. Explorez l’API via l’interface utilisateur Swagger et incorporez-la dans d’autres programmes.

> [!TIP]
> Pour utiliser l’IU Swagger à la racine de l’application (`http://localhost:<port>/`), définissez la propriété `RoutePrefix` sur une chaîne vide :
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

Si vous utilisez des répertoires avec IIS ou un proxy inverse, définissez le point de terminaison Swagger sur un chemin relatif avec le préfixe `./`. Par exemple : `./swagger/v1/swagger.json`. L’utilisation de `/swagger/v1/swagger.json` indique à l’application de rechercher le fichier JSON à la racine réelle de l’URL (plus le préfixe de la route s’il est utilisé). Par exemple, utilisez `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` au lieu de `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.

## <a name="customize-and-extend"></a>Personnaliser et étendre

Swagger fournit des options pour documenter le modèle objet et personnaliser l’interface utilisateur en fonction de votre thème.

Dans la classe `Startup`, ajoutez les espaces de noms suivants :

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>Informations sur l’API et description

L’action de configuration transmise à la méthode `AddSwaggerGen` ajoute des informations comme l’auteur, la license et la description :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

L’IU Swagger affiche les informations de la version :

![Interface utilisateur de Swagger avec les informations de version : description, auteur et lien pour en savoir plus](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>commentaires XML

Vous pouvez activer les commentaires XML en adoptant l’une des approches suivantes :

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis sélectionnez **Modifier <nom_projet>.csproj**.
* Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Dans **Explorateur de solutions** , cliquez avec le bouton droit sur le projet, puis sélectionnez **Propriétés**.
* Cochez la case **fichier de documentation XML** sous la section **sortie** de l’onglet **générer** .

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* Dans le *Panneau Solutions*, appuyez sur **contrôle** et cliquez sur le nom du projet. Accédez à **Outils** > **modifier le fichier**.
* Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Ouvrez la boîte de dialogue **Options du projet** > **Générer** > **Compilateur**
* Cochez la case **générer la documentation XML** sous la section **Options générales** .

::: moniker-end

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

L’activation de commentaires XML fournit des informations de débogage sur les membres et types publics non documentés. Les membres et types non documentés sont indiqués par le message d’avertissement. Par exemple, le message suivant indique une violation du code d’avertissement 1591 :

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Pour supprimer des avertissements à l’échelle d’un projet, définissez une liste séparée par des points-virgules de codes d’avertissement à ignorer dans le fichier de projet. L’ajout des codes d’avertissement à `$(NoWarn);` applique aussi les [valeurs par défaut C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

Pour supprimer des avertissements uniquement pour des membres spécifiques, placez le code dans les directives de préprocesseur [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning). Cette approche est utile pour le code qui ne doit pas être exposé via les docs de l’API. Dans l’exemple suivant, le code d’avertissement CS1591 est ignoré pour `Program` la classe entière. La mise en œuvre de code d’avertissement est restaurée à la fin de la définition de classe. Spécifier plusieurs codes d’avertissement avec une liste délimitée par des virgules.

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

Configurez Swagger de façon à utiliser le fichier XML généré avec les instructions précédentes. Pour les systèmes d’exploitation Linux ou non-Windows, les chemins et les noms de fichiers peuvent respecter la casse. Par exemple, un fichier *ToDoApi.XML* est valide sur Windows, mais pas sur CentOS.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

Dans le code précédent, la [réflexion](/dotnet/csharp/programming-guide/concepts/reflection) est utilisée pour générer un nom de fichier XML correspondant à celui du projet d’API Web. La propriété [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) est utilisée pour construire le chemin du fichier XML. Certaines fonctionnalités de Swagger (par exemple, les schémas de paramètres d’entrée ou les méthodes HTTP et les codes de réponse issus des attributs respectifs) fonctionnent sans fichier de documentation XML. Pour la plupart des fonctionnalités cependant, à savoir les résumés de méthode et les descriptions des paramètres et des codes de réponse, l’utilisation d’un fichier XML est obligatoire.

Quand vous ajoutez des commentaires avec trois barres obliques à une action, la description est ajoutée à l’en-tête de section dans l’interface utilisateur Swagger. Ajoutez un élément de [ \<synthèse>](/dotnet/csharp/programming-guide/xmldoc/summary) au `Delete` -dessus de l’action :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

L’IU Swagger affiche le texte interne de l’élément `<summary>` du code précédent :

![Interface utilisateur de Swagger affichant le commentaire XML « Deletes a specific TodoItem. » pour la méthode DELETE.](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

L’IU est définie par le schéma JSON généré :

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

Ajoutez un [ \<élément notes>](/dotnet/csharp/programming-guide/xmldoc/remarks) à la `Create` documentation de la méthode d’action. Il complète les informations spécifiées dans l’élément `<summary>` et fournit une interface utilisateur Swagger plus robuste. Le contenu de l’élément `<remarks>` peut être du texte, du code JSON ou du code XML.

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

Notez les améliorations de l’IU avec ces commentaires supplémentaires :

![Interface utilisateur de Swagger avec des commentaires supplémentaires affichés](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Annotations de données

Marquez le modèle avec des attributs, qui se trouvent dans l’espace de noms [System. ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) , pour aider à piloter les composants de l’interface utilisateur Swagger.

Ajoutez l’attribut `[Required]` à la propriété `Name` de la classe `TodoItem` :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

La présence de cet attribut change le comportement de l’interface utilisateur et modifie le schéma JSON sous-jacent :

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

Ajoutez l’attribut `[Produces("application/json")]` au contrôleur d’API. Son objectif est de déclarer que les actions du contrôleur prennent en charge une réponse dont le type de contenu est *application/json* :

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

La zone de liste déroulante **Response Content Type** permet de sélectionner ce type de contenu comme valeur par défaut pour les actions GET du contrôleur :

![Interface utilisateur de Swagger avec le type de contenu de réponse par défaut](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

À mesure que l’utilisation des annotations de données dans l’API web augmente, l’interface utilisateur et les pages d’aide de l’API deviennent de plus en plus descriptives et utiles.

### <a name="describe-response-types"></a>Décrire des types de réponse

Les développeurs consommant une API web s’intéressent surtout à ce qui est retourné&mdash;, en particulier les types de réponse et les codes d’erreur (s’ils ne sont pas standards). Les types de réponse et les codes d’erreur sont décrits dans les commentaires XML et les annotations de données.

L’action `Create` retourne un code d’état HTTP 201 en cas de réussite. Un code d’état HTTP 400 est retourné quand le corps de la demande postée est null. Sans documentation appropriée dans l’interface utilisateur de Swagger, le consommateur n’a pas connaissance de ces résultats attendus. Pour résoudre ce problème, ajoutez les lignes en surbrillance de l’exemple suivant :

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

L’interface utilisateur de Swagger documente maintenant clairement les codes de réponse HTTP attendus :

![Interface utilisateur de Swagger affichant la description de la classe de réponse POST « Returns the newly created Todo item » et « 400 - If the item is null » pour le code d’état et la raison sous Response Messages](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

Dans ASP.NET Core 2.2 ou une version ultérieure, les conventions peuvent être utilisées comme alternatives à la décoration explicites des actions individuelles avec `[ProducesResponseType]`. Pour plus d’informations, consultez <xref:web-api/advanced/conventions>.

::: moniker-end

### <a name="customize-the-ui"></a>Personnaliser l’interface utilisateur

L’interface utilisateur est fonctionnelle et conviviale. Toutefois, les pages de documentation d’API doivent représenter votre marque ou thème. La personnalisation des composants Swashbuckle nécessite d’ajouter les ressources qui traitent les fichiers statiques et de générer la structure de dossiers pour héberger ces fichiers.

Si vous ciblez le .NET Framework ou .NET Core 1.x, ajoutez le package NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) au projet :

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

Le package NuGet précédent est déjà installé si vous ciblez .NET Core 2.x et que vous utilisez le [métapackage](xref:fundamentals/metapackage).

Activez le middleware de fichiers statiques :

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

Faites l’acquisition du contenu du dossier *dist* à partir du [dépôt GitHub de l’interface utilisateur de Swagger](https://github.com/swagger-api/swagger-ui/tree/master/dist). Ce dossier contient les ressources nécessaires pour la page de l’interface utilisateur de Swagger.

Créez un dossier *wwwroot/swagger/ui* et copiez dedans le contenu du dossier *dist*.

Créez un fichier *custom.css* dans *wwwroot/swagger/ui* avec le code CSS suivant pour personnaliser l’en-tête de page :

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

Référencez *custom.css* dans le fichier *index.html* situé à l’intérieur du dossier UI, à la suite de n’importe quel autre fichier CSS :

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

Accédez à la page *index.html* à l’adresse `http://localhost:<port>/swagger/ui/index.html`. Entrez `https://localhost:<port>/swagger/v1/swagger.json` dans la zone de texte de l’en-tête, puis cliquez sur le bouton **Explorer**. La page résultante ressemble à ceci :

![Interface utilisateur de Swagger avec titre d’en-tête personnalisé](web-api-help-pages-using-swagger/_static/custom-header.png)

Vous pouvez faire encore beaucoup avec cette page. Pour découvrir toutes les fonctionnalités pour les ressources d’interface utilisateur, accédez au [dépôt GitHub de l’interface utilisateur de Swagger](https://github.com/swagger-api/swagger-ui).
