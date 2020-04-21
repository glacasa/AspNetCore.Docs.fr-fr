---
title: Stockage sécurisé des secrets d’applications en développement dans ASP.NET Core
author: rick-anderson
description: Découvrez comment stocker et récupérer des informations sensibles comme secrets d’applications lors du développement d’une application ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: 9d4e59c003afc253971ee64fce523c7188d3582a
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661805"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Stockage sécurisé des secrets d’applications en développement dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT), Kirk [Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), et Scott [Addie](https://github.com/scottaddie)

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

Ce document explique les techniques de stockage et de récupération de données sensibles lors du développement d’une application ASP.NET Core sur une machine de développement. Ne stockez jamais de mots de passe ou d’autres données sensibles dans le code source. Les secrets de production ne doivent pas être utilisés pour le développement ou le test. Les secrets ne doivent pas être déployés avec l’application. Au lieu de cela, des secrets devraient être mis à disposition dans l’environnement de production par le biais d’un moyen contrôlé comme les variables de l’environnement, Azure Key Vault, etc. Vous pouvez stocker et protéger les secrets de test et de production Azure avec le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variables d'environnement

Les variables de l’environnement sont utilisées pour éviter le stockage des secrets d’application dans le code ou dans les fichiers de configuration locaux. Les variables de l’environnement remplacent les valeurs de configuration pour toutes les sources de configuration précédemment spécifiées.

Considérez une application web ASP.NET Core dans laquelle la sécurité **des comptes utilisateur individuels** est activée. Une chaîne de connexion de base de données par défaut est incluse `DefaultConnection`dans le fichier *appsettings.json* du projet avec la clé . La chaîne de connexion par défaut est pour LocalDB, qui s’exécute en mode utilisateur et ne nécessite pas de mot de passe. Pendant le déploiement `DefaultConnection` de l’application, la valeur clé peut être remplacée par la valeur d’une variable d’environnement. La variable d’environnement peut stocker la chaîne de connexion complète avec des informations d’identification sensibles.

> [!WARNING]
> Les variables de l’environnement sont généralement stockées dans un texte simple et non chiffré. Si la machine ou le processus est compromis, les variables de l’environnement peuvent être consultées par des parties non fiables. Des mesures supplémentaires pour empêcher la divulgation de secrets d’utilisateur peuvent être nécessaires.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gestionnaire secret

L’outil Secret Manager stocke des données sensibles lors du développement d’un projet ASP.NET Core. Dans ce contexte, un élément de données sensibles est un secret d’application. Les secrets de l’application sont stockés dans un endroit séparé de l’arbre du projet. Les secrets de l’application sont associés à un projet spécifique ou partagés sur plusieurs projets. Les secrets de l’application ne sont pas vérifiés dans le contrôle source.

> [!WARNING]
> L’outil Secret Manager ne crypte pas les secrets stockés et ne doit pas être traité comme un magasin de confiance. C’est uniquement à des fins de développement. Les clés et les valeurs sont stockées dans un fichier de configuration JSON dans l’annuaire du profil utilisateur.

## <a name="how-the-secret-manager-tool-works"></a>Fonctionnement de l’outil Secret Manager

L’outil Secret Manager résume les détails de l’implémentation, tels que l’endroit et la façon dont les valeurs sont stockées. Vous pouvez utiliser l’outil sans connaître ces détails d’implémentation. Les valeurs sont stockées dans un fichier de configuration JSON dans un dossier de profil utilisateur protégé par le système sur la machine locale :

# <a name="windows"></a>[Windows](#tab/windows)

Chemin du système de fichiers :

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Chemin du système de fichiers :

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Dans les chemins de `<user_secrets_id>` fichier `UserSecretsId` précédents, remplacez par la valeur spécifiée dans le fichier *.csproj.*

N’écrivez pas de code qui dépend de l’emplacement ou du format des données enregistrées avec l’outil Secret Manager. Ces détails de mise en œuvre peuvent changer. Par exemple, les valeurs secrètes ne sont pas cryptées, mais pourraient l’être à l’avenir.

## <a name="enable-secret-storage"></a>Activer le stockage secret

L’outil Secret Manager fonctionne sur des paramètres de configuration spécifiques à un projet stockés dans votre profil d’utilisateur.

L’outil Secret `init` Manager comprend une commande en .NET Core SDK 3.0.100 ou plus tard. Pour utiliser les secrets d’utilisateur, exécutez la commande suivante dans l’annuaire du projet :

```dotnetcli
dotnet user-secrets init
```

La commande précédente `UserSecretsId` ajoute `PropertyGroup` un élément dans un fichier *.csproj.* Par défaut, le `UserSecretsId` texte interne est un GUID. Le texte intérieur est arbitraire, mais est unique au projet.

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

Dans Visual Studio, cliquez à droite sur le projet dans Solution Explorer, et **sélectionnez Gérer les secrets d’utilisateur** dans le menu contextuelle. Ce geste `UserSecretsId` ajoute un élément, peuplé d’un GUID, au fichier *.csproj.*

## <a name="set-a-secret"></a>Définir un secret

Définissez un secret d’application composé d’une clé et de sa valeur. Le secret est associé à `UserSecretsId` la valeur du projet. Par exemple, exécutez la commande suivante à partir de l’annuaire dans lequel le fichier *.csproj* existe :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Dans l’exemple précédent, le `Movies` côlon dénote qui `ServiceApiKey` est un objet littéral avec une propriété.

L’outil Secret Manager peut également être utilisé à partir d’autres répertoires. Utilisez `--project` l’option pour fournir la trajectoire du système de fichiers à laquelle le fichier *.csproj* existe. Par exemple :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>La structure JSON aplatit dans Visual Studio

Visual Studio’s **Manage User Secrets** geste ouvre un fichier *secrets.json* dans l’éditeur de texte. Remplacez le contenu de *secrets.json* par les paires de valeur clé à stocker. Par exemple :

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

La structure JSON est aplatie `dotnet user-secrets remove` `dotnet user-secrets set`après modifications via ou . Par exemple, `dotnet user-secrets remove "Movies:ConnectionString"` la `Movies` course s’effondre l’objet littéral. Le fichier modifié ressemble à ce qui suit :

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Définir plusieurs secrets

Un lot de secrets peut être réglé `set` en tuyautant JSON à la commande. Dans l’exemple suivant, le contenu du fichier *input.json* est acheminé à la `set` commande.

# <a name="windows"></a>[Windows](#tab/windows)

Ouvrez une coque de commande et exécutez la commande suivante :

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Ouvrez une coque de commande et exécutez la commande suivante :

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Accéder à un secret

[L’API ASP.NET Core Configuration](xref:fundamentals/configuration/index) donne accès aux secrets de Secret Manager.

Dans ASP.NET Core 2.0 ou plus tard, la source de configuration des <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> secrets d’utilisateur est automatiquement ajoutée en mode de développement lorsque le projet appelle à parasimer une nouvelle instance de l’hôte avec des défauts préconfigurés. `CreateDefaultBuilder`appels <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> lorsque <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>le est :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Lorsqu’il `CreateDefaultBuilder` n’est pas appelé, ajoutez <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> explicitement `Startup` la source de configuration des secrets d’utilisateur en appelant le constructeur. N’appelez `AddUserSecrets` que lorsque l’application s’exécute dans l’environnement développement, comme le montre l’exemple suivant :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

Les secrets d’utilisateur `Configuration` peuvent être récupérés via l’API :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]


## <a name="map-secrets-to-a-poco"></a>Secrets de carte à un POCO

Cartographier un objet entier littéral à un POCO (une simple classe .NET avec propriétés) est utile pour agréger les propriétés connexes.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Pour cartographier les secrets précédents à `Configuration` un OCO, utilisez la fonction de liaison du [graphique](xref:fundamentals/configuration/index#bind-to-an-object-graph) objet de l’API. Le code suivant se `MovieSettings` lie à un `ServiceApiKey` OCO personnalisé et accède à la valeur de la propriété :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Les `Movies:ConnectionString` `Movies:ServiceApiKey` secrets et les secrets sont `MovieSettings`cartographiés sur les propriétés respectives dans :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Remplacement de cordes avec des secrets

Stocker des mots de passe en texte clair n’est pas sûr. Par exemple, une chaîne de connexion de base de données stockée dans *appsettings.json* peut inclure un mot de passe pour l’utilisateur spécifié :

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Une approche plus sûre consiste à stocker le mot de passe comme secret. Par exemple :

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Retirez `Password` la paire de valeur de clé de la chaîne de connexion dans *appsettings.json*. Par exemple :

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

La valeur du secret peut <xref:System.Data.SqlClient.SqlConnectionStringBuilder> être fixée <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> sur la propriété d’un objet pour compléter la chaîne de connexion :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Énumérer les secrets

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Exécutez la commande suivante de l’annuaire dans lequel le fichier *.csproj* existe :

```dotnetcli
dotnet user-secrets list
```

Vous obtenez la sortie suivante :

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Dans l’exemple précédent, un côlon dans les noms clés dénote la hiérarchie de l’objet dans *secrets.json*.

## <a name="remove-a-single-secret"></a>Supprimer un seul secret

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Exécutez la commande suivante de l’annuaire dans lequel le fichier *.csproj* existe :

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Le fichier *secrets.json* de l’application a été modifié `MoviesConnectionString` pour supprimer la paire de valeur clé associée à la clé :

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`affiche le message suivant :

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Supprimer tous les secrets

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Exécutez la commande suivante de l’annuaire dans lequel le fichier *.csproj* existe :

```dotnetcli
dotnet user-secrets clear
```

Tous les secrets d’utilisateur de l’application ont été supprimés du fichier *secrets.json:*

```json
{}
```

Exécution `dotnet user-secrets list` affiche le message suivant:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Ressources supplémentaires

* Voir [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/16328) pour plus d’informations sur l’accès Secret Manager à partir de l’IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT), Daniel [Roth](https://github.com/danroth27), et [Scott Addie](https://github.com/scottaddie)

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

Ce document explique les techniques de stockage et de récupération de données sensibles lors du développement d’une application ASP.NET Core sur une machine de développement. Ne stockez jamais de mots de passe ou d’autres données sensibles dans le code source. Les secrets de production ne doivent pas être utilisés pour le développement ou le test. Les secrets ne doivent pas être déployés avec l’application. Au lieu de cela, des secrets devraient être mis à disposition dans l’environnement de production par le biais d’un moyen contrôlé comme les variables de l’environnement, Azure Key Vault, etc. Vous pouvez stocker et protéger les secrets de test et de production Azure avec le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variables d'environnement

Les variables de l’environnement sont utilisées pour éviter le stockage des secrets d’application dans le code ou dans les fichiers de configuration locaux. Les variables de l’environnement remplacent les valeurs de configuration pour toutes les sources de configuration précédemment spécifiées.

Considérez une application web ASP.NET Core dans laquelle la sécurité **des comptes utilisateur individuels** est activée. Une chaîne de connexion de base de données par défaut est incluse `DefaultConnection`dans le fichier *appsettings.json* du projet avec la clé . La chaîne de connexion par défaut est pour LocalDB, qui s’exécute en mode utilisateur et ne nécessite pas de mot de passe. Pendant le déploiement `DefaultConnection` de l’application, la valeur clé peut être remplacée par la valeur d’une variable d’environnement. La variable d’environnement peut stocker la chaîne de connexion complète avec des informations d’identification sensibles.

> [!WARNING]
> Les variables de l’environnement sont généralement stockées dans un texte simple et non chiffré. Si la machine ou le processus est compromis, les variables de l’environnement peuvent être consultées par des parties non fiables. Des mesures supplémentaires pour empêcher la divulgation de secrets d’utilisateur peuvent être nécessaires.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gestionnaire secret

L’outil Secret Manager stocke des données sensibles lors du développement d’un projet ASP.NET Core. Dans ce contexte, un élément de données sensibles est un secret d’application. Les secrets de l’application sont stockés dans un endroit séparé de l’arbre du projet. Les secrets de l’application sont associés à un projet spécifique ou partagés sur plusieurs projets. Les secrets de l’application ne sont pas vérifiés dans le contrôle source.

> [!WARNING]
> L’outil Secret Manager ne crypte pas les secrets stockés et ne doit pas être traité comme un magasin de confiance. C’est uniquement à des fins de développement. Les clés et les valeurs sont stockées dans un fichier de configuration JSON dans l’annuaire du profil utilisateur.

## <a name="how-the-secret-manager-tool-works"></a>Fonctionnement de l’outil Secret Manager

L’outil Secret Manager résume les détails de l’implémentation, tels que l’endroit et la façon dont les valeurs sont stockées. Vous pouvez utiliser l’outil sans connaître ces détails d’implémentation. Les valeurs sont stockées dans un fichier de configuration JSON dans un dossier de profil utilisateur protégé par le système sur la machine locale :

# <a name="windows"></a>[Windows](#tab/windows)

Chemin du système de fichiers :

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Chemin du système de fichiers :

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Dans les chemins de `<user_secrets_id>` fichier `UserSecretsId` précédents, remplacez par la valeur spécifiée dans le fichier *.csproj.*

N’écrivez pas de code qui dépend de l’emplacement ou du format des données enregistrées avec l’outil Secret Manager. Ces détails de mise en œuvre peuvent changer. Par exemple, les valeurs secrètes ne sont pas cryptées, mais pourraient l’être à l’avenir.

## <a name="enable-secret-storage"></a>Activer le stockage secret

L’outil Secret Manager fonctionne sur des paramètres de configuration spécifiques à un projet stockés dans votre profil d’utilisateur.

Pour utiliser les secrets `UserSecretsId` d’utilisateur, définissez un élément dans un `PropertyGroup` fichier *.csproj.* Le texte `UserSecretsId` intérieur est arbitraire, mais est unique au projet. Les développeurs génèrent généralement `UserSecretsId`un GUID pour le .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> Dans Visual Studio, cliquez à droite sur le projet dans Solution Explorer, et **sélectionnez Gérer les secrets d’utilisateur** dans le menu contextuelle. Ce geste `UserSecretsId` ajoute un élément, peuplé d’un GUID, au fichier *.csproj.*

## <a name="set-a-secret"></a>Définir un secret

Définissez un secret d’application composé d’une clé et de sa valeur. Le secret est associé à `UserSecretsId` la valeur du projet. Par exemple, exécutez la commande suivante à partir de l’annuaire dans lequel le fichier *.csproj* existe :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Dans l’exemple précédent, le `Movies` côlon dénote qui `ServiceApiKey` est un objet littéral avec une propriété.

L’outil Secret Manager peut également être utilisé à partir d’autres répertoires. Utilisez `--project` l’option pour fournir la trajectoire du système de fichiers à laquelle le fichier *.csproj* existe. Par exemple :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>La structure JSON aplatit dans Visual Studio

Visual Studio’s **Manage User Secrets** geste ouvre un fichier *secrets.json* dans l’éditeur de texte. Remplacez le contenu de *secrets.json* par les paires de valeur clé à stocker. Par exemple :

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

La structure JSON est aplatie `dotnet user-secrets remove` `dotnet user-secrets set`après modifications via ou . Par exemple, `dotnet user-secrets remove "Movies:ConnectionString"` la `Movies` course s’effondre l’objet littéral. Le fichier modifié ressemble à ce qui suit :

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Définir plusieurs secrets

Un lot de secrets peut être réglé `set` en tuyautant JSON à la commande. Dans l’exemple suivant, le contenu du fichier *input.json* est acheminé à la `set` commande.

# <a name="windows"></a>[Windows](#tab/windows)

Ouvrez une coque de commande et exécutez la commande suivante :

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Ouvrez une coque de commande et exécutez la commande suivante :

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Accéder à un secret

[L’API ASP.NET Core Configuration](xref:fundamentals/configuration/index) donne accès aux secrets de Secret Manager.

Si votre projet cible .NET Framework, installez le package [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.


Dans ASP.NET Core 2.0 ou plus tard, la source de configuration des <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> secrets d’utilisateur est automatiquement ajoutée en mode de développement lorsque le projet appelle à parasimer une nouvelle instance de l’hôte avec des défauts préconfigurés. `CreateDefaultBuilder`appels <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> lorsque <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>le est :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]


Lorsqu’il `CreateDefaultBuilder` n’est pas appelé, ajoutez <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> explicitement `Startup` la source de configuration des secrets d’utilisateur en appelant le constructeur. N’appelez `AddUserSecrets` que lorsque l’application s’exécute dans l’environnement développement, comme le montre l’exemple suivant :

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

Les secrets d’utilisateur `Configuration` peuvent être récupérés via l’API :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Secrets de carte à un POCO

Cartographier un objet entier littéral à un POCO (une simple classe .NET avec propriétés) est utile pour agréger les propriétés connexes.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Pour cartographier les secrets précédents à `Configuration` un OCO, utilisez la fonction de liaison du [graphique](xref:fundamentals/configuration/index#bind-to-an-object-graph) objet de l’API. Le code suivant se `MovieSettings` lie à un `ServiceApiKey` OCO personnalisé et accède à la valeur de la propriété :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Les `Movies:ConnectionString` `Movies:ServiceApiKey` secrets et les secrets sont `MovieSettings`cartographiés sur les propriétés respectives dans :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Remplacement de cordes avec des secrets

Stocker des mots de passe en texte clair n’est pas sûr. Par exemple, une chaîne de connexion de base de données stockée dans *appsettings.json* peut inclure un mot de passe pour l’utilisateur spécifié :

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Une approche plus sûre consiste à stocker le mot de passe comme secret. Par exemple :

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Retirez `Password` la paire de valeur de clé de la chaîne de connexion dans *appsettings.json*. Par exemple :

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

La valeur du secret peut <xref:System.Data.SqlClient.SqlConnectionStringBuilder> être fixée <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> sur la propriété d’un objet pour compléter la chaîne de connexion :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Énumérer les secrets

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Exécutez la commande suivante de l’annuaire dans lequel le fichier *.csproj* existe :

```dotnetcli
dotnet user-secrets list
```

Vous obtenez la sortie suivante :

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Dans l’exemple précédent, un côlon dans les noms clés dénote la hiérarchie de l’objet dans *secrets.json*.

## <a name="remove-a-single-secret"></a>Supprimer un seul secret

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Exécutez la commande suivante de l’annuaire dans lequel le fichier *.csproj* existe :

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Le fichier *secrets.json* de l’application a été modifié `MoviesConnectionString` pour supprimer la paire de valeur clé associée à la clé :

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Exécution `dotnet user-secrets list` affiche le message suivant:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Supprimer tous les secrets

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Exécutez la commande suivante de l’annuaire dans lequel le fichier *.csproj* existe :

```dotnetcli
dotnet user-secrets clear
```

Tous les secrets d’utilisateur de l’application ont été supprimés du fichier *secrets.json:*

```json
{}
```

Exécution `dotnet user-secrets list` affiche le message suivant:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Ressources supplémentaires

* Voir [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/16328) pour plus d’informations sur l’accès Secret Manager à partir de l’IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end