---
title: Tests unitaires Razor Pages dans ASP.NET Core
author: rick-anderson
description: Découvrez comment créer des tests unitaires pour les applications Razor Pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: 0e217b6b7f15519a3da44f5d074cf80fa96a3b3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664408"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Tests unitaires Razor Pages dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core prend en charge les tests unitaires des applications Razor Pages. Les tests de la couche d’accès aux données (DAL) et des modèles de pages permettent d’assurer :

* Certaines parties d’une application Razor Pages fonctionnent indépendamment et ensemble en tant qu’unité pendant la construction de l’application.
* Les classes et les méthodes ont une portée limitée de responsabilité.
* Il existe des documents supplémentaires sur la façon dont l’application doit se comporter.
* Des régressions, qui sont des erreurs provoquées par des mises à jour du code, se trouvent lors du bâtiment et du déploiement automatisés.

Ce sujet suppose que vous avez une compréhension de base des applications Razor Pages et des tests unitaires. Si vous n’êtes pas familier avec les applications Razor Pages ou les concepts de test, consultez les sujets suivants :

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Effectuer des tests unitaires de C# dans .NET Core à l’aide de dotnet test et de xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

Le projet d’exemple est composé de deux applications :

| Application         | Dossier de projet                     | Description |
| ----------- | ---------------------------------- | ----------- |
| Application de message | *src/RazorPagesTestSample*         | Permet à un utilisateur d’ajouter un message, de supprimer un message, de supprimer tous les messages et d’analyser les messages (trouver le nombre moyen de mots par message). |
| Tester une application    | *tests/RazorPagesTestSample.Tests* | Utilisé pour tester le modèle de page DAL et Index de l’application de message. |

Les tests peuvent être exécutés à l’aide des caractéristiques de test intégrées d’un IDE, tels que [Visual Studio](/visualstudio/test/unit-test-your-code) ou Visual Studio [pour Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Si vous utilisez [visual Studio Code](https://code.visualstudio.com/) ou la ligne de commande, exécutez la commande suivante à une invite de commande dans le dossier *tests/RazorPagesTestSample.Tests* :

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organisation d’applications de message

L’application de message est un système de messagerie Razor Pages avec les caractéristiques suivantes :

* La page d’index de l’application *(Pages/Index.cshtml* et *Pages/Index.cshtml.cs*) fournit une interface utilisateur et des méthodes de modèle de page pour contrôler l’ajout, la suppression et l’analyse des messages (trouver le nombre moyen de mots par message).
* Un message est `Message` décrit par la classe (*Data/Message.cs*) avec deux propriétés: `Id` (clé) et `Text` (message). La `Text` propriété est requise et limitée à 200 caractères.
* Les messages sont stockés [à l’aide de la base de données en mémoire d’Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* L’application contient un DAL dans `AppDbContext` sa classe de contexte de base de données, (*Data/AppDbContext.cs*). Les méthodes DAL `virtual`sont marquées, ce qui permet de se moquer des méthodes d’utilisation dans les tests.
* Si la base de données est vide sur le démarrage de l’application, le magasin de messages est paraséfié avec trois messages. Ces *messages ensemencés* sont également utilisés dans les tests.

&#8224;Le sujet EF, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explique comment utiliser une base de données en mémoire pour les tests avec MSTest. Ce sujet utilise le cadre de test [xUnit.](https://xunit.github.io/) Les concepts de test et les implémentations de tests dans différents cadres de test sont similaires mais non identiques.

Bien que l’application d’échantillon n’utilise pas le modèle de dépôt et n’est pas un exemple efficace du [modèle de l’unité de travail (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Razor Pages prend en charge ces modèles de développement. Pour plus d’informations, voir [Concevoir la couche de persistance de l’infrastructure](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) et <xref:mvc/controllers/testing> (l’échantillon met en œuvre le modèle de dépôt).

## <a name="test-app-organization"></a>Organisation d’applications de test

L’application de test est une application console à l’intérieur du dossier *tests/RazorPagesTestSample.Tests.*

| Dossier d’application de test | Description |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* contient les tests unitaires pour le DAL.</li><li>*IndexPageTests.cs* contient les tests unitaires pour le modèle de page Index.</li></ul> |
| *Services*     | Contient `TestDbContextOptions` la méthode utilisée pour créer de nouvelles options de contexte de base de données pour chaque test unitaire DAL afin que la base de données soit réinitialisée à son état de base pour chaque test. |

Le cadre d’essai est [xUnit](https://xunit.github.io/). Le cadre de moquerie d’objet est [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Tests unitaires de la couche d’accès aux données (DAL)

L’application de message a un `AppDbContext` DAL avec quatre méthodes contenues dans la classe (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Chaque méthode comporte un ou deux tests unitaires dans l’application de test.

| Méthode DAL               | Fonction                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Obtient un `List<Message>` de la base `Text` de données triée par la propriété. |
| `AddMessageAsync`        | Ajoute `Message` un à la base de données.                                          |
| `DeleteAllMessagesAsync` | Supprime toutes `Message` les entrées de la base de données.                           |
| `DeleteMessageAsync`     | Supprime un `Message` single de `Id`la base de données par .                      |

Les tests unitaires du DAL nécessitent <xref:Microsoft.EntityFrameworkCore.DbContextOptions> lors de la création d’un nouveau `AppDbContext` pour chaque test. Une approche pour `DbContextOptions` créer le pour <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>chaque test est d’utiliser un :

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Le problème avec cette approche est que chaque test reçoit la base de données dans n’importe quel état du test précédent l’a laissé. Cela peut être problématique lorsque vous essayez d’écrire des tests d’unité atomique qui n’interfèrent pas les uns avec les autres. Pour forcer `AppDbContext` l’utilisation d’un nouveau contexte `DbContextOptions` de base de données pour chaque test, fournissez une instance basée sur un nouveau fournisseur de services. L’application de test montre `Utilities` comment `TestDbContextOptions` le faire en utilisant sa méthode de classe *(tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

L’utilisation des `DbContextOptions` tests unitaires DAL permet à chaque essai de fonctionner atomiquement avec une nouvelle instance de base de données :

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Chaque méthode de `DataAccessLayerTest` test dans la classe *(UnitTests/DataAccessLayerTest.cs*) suit un modèle similaire d’Arrangement-Act-Assert :

1. Disposer : La base de données est configurée pour le test et/ou le résultat attendu est défini.
1. Acte : Le test est exécuté.
1. Assert: Des affirmations sont faites pour déterminer si le résultat du test est un succès.

Par exemple, `DeleteMessageAsync` la méthode est responsable de la `Id` suppression d’un seul message identifié par son (*src/RazorPagesTestSample/Data/AppDbContext.cs*) :

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Il y a deux tests pour cette méthode. Un test vérifie que la méthode supprime un message lorsque le message est présent dans la base de données. L’autre méthode teste que la base `Id` de données ne change pas si le message de suppression n’existe pas. La `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` méthode est indiquée ci-dessous :

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Tout d’abord, la méthode effectue l’étape Arrange, où la préparation de l’étape de la Loi a lieu. Les messages d’ensemencement sont obtenus et conservés dans `seedMessages`. Les messages d’ensemencement sont enregistrés dans la base de données. Le message `Id` avec `1` un de est réglé pour la suppression. Lorsque `DeleteMessageAsync` la méthode est exécutée, les messages attendus doivent avoir `Id` tous `1`les messages, sauf pour celui avec un de . La `expectedMessages` variable représente ce résultat attendu.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

La méthode agit: La `DeleteMessageAsync` méthode est `recId` `1`exécutée en passant dans le de :

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Enfin, la méthode `Messages` obtient le du contexte et `expectedMessages` la compare à l’affirmation que les deux sont égaux:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Afin de comparer que `List<Message>` les deux sont les mêmes:

* Les messages sont `Id`commandés par .
* Les paires de `Text` messages sont comparées sur la propriété.

Une méthode de `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` test similaire, vérifie le résultat de la tentative de supprimer un message qui n’existe pas. Dans ce cas, les messages attendus dans la base `DeleteMessageAsync` de données doivent être égaux aux messages réels après l’exécution de la méthode. Il ne devrait pas y avoir de modification au contenu de la base de données :

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Tests unitaires des méthodes du modèle de page

Un autre ensemble de tests unitaires est responsable des tests des méthodes du modèle de page. Dans l’application de message, les `IndexModel` modèles de page Index se trouvent dans la classe dans *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Méthode de modèle de page | Fonction |
| ----------------- | -------- |
| `OnGetAsync` | Obtient les messages du DAL pour l’interface utilisateur en utilisant la `GetMessagesAsync` méthode. |
| `OnPostAddMessageAsync` | Si le [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) est `AddMessageAsync` valide, appelle pour ajouter un message à la base de données. |
| `OnPostDeleteAllMessagesAsync` | Appels `DeleteAllMessagesAsync` pour supprimer tous les messages dans la base de données. |
| `OnPostDeleteMessageAsync` | Exécute `DeleteMessageAsync` pour supprimer un `Id` message avec le spécifié. |
| `OnPostAnalyzeMessagesAsync` | Si un ou plusieurs messages sont dans la base de données, calcule le nombre moyen de mots par message. |

Les méthodes du modèle de page `IndexPageTests` sont testées à l’aide de sept tests dans la classe *(tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). Les tests utilisent le modèle familier Arrange-Assert-Act. Ces tests portent sur :

* Déterminer si les méthodes suivent le comportement correct lorsque le [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) est invalide.
* La confirmation des <xref:Microsoft.AspNetCore.Mvc.IActionResult>méthodes produisent le correct .
* Vérifier que les affectations de valeur de propriété sont faites correctement.

Ce groupe de tests se moque souvent des méthodes du DAL pour produire les données attendues pour l’étape de la Loi où une méthode de modèle de page est exécutée. Par exemple, `GetMessagesAsync` la `AppDbContext` méthode de la est moquée pour produire la sortie. Lorsqu’une méthode de modèle de page exécute cette méthode, le simulacre renvoie le résultat. Les données ne proviennent pas de la base de données. Cela crée des conditions de test prévisibles et fiables pour l’utilisation du DAL dans les tests de modèle de page.

Le `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test montre `GetMessagesAsync` comment la méthode est moquée pour le modèle de page:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Lorsque `OnGetAsync` la méthode est exécutée dans l’étape de `GetMessagesAsync` la Loi, elle appelle la méthode du modèle de page.

Étape de la Loi sur les tests*unitaires (tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*) :

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`méthode du `OnGetAsync` modèle de page (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

La `GetMessagesAsync` méthode dans le DAL ne renvoie pas le résultat pour cet appel de méthode. La version simulée de la méthode renvoie le résultat.

Dans `Assert` l’étape, les`actualMessages`messages réels `Messages` ( ) sont attribués à partir de la propriété du modèle de page. Une vérification de type est également effectuée lorsque les messages sont assignés. Les messages attendus et réels `Text` sont comparés par leurs propriétés. Le test affirme que `List<Message>` les deux instances contiennent les mêmes messages.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

D’autres tests dans ce groupe <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>créer <xref:Microsoft.AspNetCore.Mvc.ActionContext> des objets `PageContext`modèle `ViewDataDictionary`de `PageContext`page qui comprennent le , le , un pour établir le , a , et un . Ceux-ci sont utiles pour effectuer des tests. Par exemple, l’application `ModelState` de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> message établit une <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> erreur `OnPostAddMessageAsync` pour vérifier qu’un valide est retourné lors de l’exécution :

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Effectuer des tests unitaires de C# dans .NET Core à l’aide de dotnet test et de xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Test unitaire votre code](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Génération d’une solution .NET Core complète sur macOS à l’aide de Visual Studio pour Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Commencer avec xUnit.net: Utilisation de .NET Core avec la ligne de commande .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core prend en charge les tests unitaires des applications Razor Pages. Les tests de la couche d’accès aux données (DAL) et des modèles de pages permettent d’assurer :

* Certaines parties d’une application Razor Pages fonctionnent indépendamment et ensemble en tant qu’unité pendant la construction de l’application.
* Les classes et les méthodes ont une portée limitée de responsabilité.
* Il existe des documents supplémentaires sur la façon dont l’application doit se comporter.
* Des régressions, qui sont des erreurs provoquées par des mises à jour du code, se trouvent lors du bâtiment et du déploiement automatisés.

Ce sujet suppose que vous avez une compréhension de base des applications Razor Pages et des tests unitaires. Si vous n’êtes pas familier avec les applications Razor Pages ou les concepts de test, consultez les sujets suivants :

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Effectuer des tests unitaires de C# dans .NET Core à l’aide de dotnet test et de xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

Le projet d’exemple est composé de deux applications :

| Application         | Dossier de projet                     | Description |
| ----------- | ---------------------------------- | ----------- |
| Application de message | *src/RazorPagesTestSample*         | Permet à un utilisateur d’ajouter un message, de supprimer un message, de supprimer tous les messages et d’analyser les messages (trouver le nombre moyen de mots par message). |
| Tester une application    | *tests/RazorPagesTestSample.Tests* | Utilisé pour tester le modèle de page DAL et Index de l’application de message. |

Les tests peuvent être exécutés à l’aide des caractéristiques de test intégrées d’un IDE, tels que [Visual Studio](/visualstudio/test/unit-test-your-code) ou Visual Studio [pour Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Si vous utilisez [visual Studio Code](https://code.visualstudio.com/) ou la ligne de commande, exécutez la commande suivante à une invite de commande dans le dossier *tests/RazorPagesTestSample.Tests* :

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organisation d’applications de message

L’application de message est un système de messagerie Razor Pages avec les caractéristiques suivantes :

* La page d’index de l’application *(Pages/Index.cshtml* et *Pages/Index.cshtml.cs*) fournit une interface utilisateur et des méthodes de modèle de page pour contrôler l’ajout, la suppression et l’analyse des messages (trouver le nombre moyen de mots par message).
* Un message est `Message` décrit par la classe (*Data/Message.cs*) avec deux propriétés: `Id` (clé) et `Text` (message). La `Text` propriété est requise et limitée à 200 caractères.
* Les messages sont stockés [à l’aide de la base de données en mémoire d’Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* L’application contient un DAL dans `AppDbContext` sa classe de contexte de base de données, (*Data/AppDbContext.cs*). Les méthodes DAL `virtual`sont marquées, ce qui permet de se moquer des méthodes d’utilisation dans les tests.
* Si la base de données est vide sur le démarrage de l’application, le magasin de messages est paraséfié avec trois messages. Ces *messages ensemencés* sont également utilisés dans les tests.

&#8224;Le sujet EF, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explique comment utiliser une base de données en mémoire pour les tests avec MSTest. Ce sujet utilise le cadre de test [xUnit.](https://xunit.github.io/) Les concepts de test et les implémentations de tests dans différents cadres de test sont similaires mais non identiques.

Bien que l’application d’échantillon n’utilise pas le modèle de dépôt et n’est pas un exemple efficace du [modèle de l’unité de travail (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Razor Pages prend en charge ces modèles de développement. Pour plus d’informations, voir [Concevoir la couche de persistance de l’infrastructure](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) et <xref:mvc/controllers/testing> (l’échantillon met en œuvre le modèle de dépôt).

## <a name="test-app-organization"></a>Organisation d’applications de test

L’application de test est une application console à l’intérieur du dossier *tests/RazorPagesTestSample.Tests.*

| Dossier d’application de test | Description |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* contient les tests unitaires pour le DAL.</li><li>*IndexPageTests.cs* contient les tests unitaires pour le modèle de page Index.</li></ul> |
| *Services*     | Contient `TestDbContextOptions` la méthode utilisée pour créer de nouvelles options de contexte de base de données pour chaque test unitaire DAL afin que la base de données soit réinitialisée à son état de base pour chaque test. |

Le cadre d’essai est [xUnit](https://xunit.github.io/). Le cadre de moquerie d’objet est [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Tests unitaires de la couche d’accès aux données (DAL)

L’application de message a un `AppDbContext` DAL avec quatre méthodes contenues dans la classe (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Chaque méthode comporte un ou deux tests unitaires dans l’application de test.

| Méthode DAL               | Fonction                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Obtient un `List<Message>` de la base `Text` de données triée par la propriété. |
| `AddMessageAsync`        | Ajoute `Message` un à la base de données.                                          |
| `DeleteAllMessagesAsync` | Supprime toutes `Message` les entrées de la base de données.                           |
| `DeleteMessageAsync`     | Supprime un `Message` single de `Id`la base de données par .                      |

Les tests unitaires du DAL nécessitent <xref:Microsoft.EntityFrameworkCore.DbContextOptions> lors de la création d’un nouveau `AppDbContext` pour chaque test. Une approche pour `DbContextOptions` créer le pour <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>chaque test est d’utiliser un :

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Le problème avec cette approche est que chaque test reçoit la base de données dans n’importe quel état du test précédent l’a laissé. Cela peut être problématique lorsque vous essayez d’écrire des tests d’unité atomique qui n’interfèrent pas les uns avec les autres. Pour forcer `AppDbContext` l’utilisation d’un nouveau contexte `DbContextOptions` de base de données pour chaque test, fournissez une instance basée sur un nouveau fournisseur de services. L’application de test montre `Utilities` comment `TestDbContextOptions` le faire en utilisant sa méthode de classe *(tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

L’utilisation des `DbContextOptions` tests unitaires DAL permet à chaque essai de fonctionner atomiquement avec une nouvelle instance de base de données :

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Chaque méthode de `DataAccessLayerTest` test dans la classe *(UnitTests/DataAccessLayerTest.cs*) suit un modèle similaire d’Arrangement-Act-Assert :

1. Disposer : La base de données est configurée pour le test et/ou le résultat attendu est défini.
1. Acte : Le test est exécuté.
1. Assert: Des affirmations sont faites pour déterminer si le résultat du test est un succès.

Par exemple, `DeleteMessageAsync` la méthode est responsable de la `Id` suppression d’un seul message identifié par son (*src/RazorPagesTestSample/Data/AppDbContext.cs*) :

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Il y a deux tests pour cette méthode. Un test vérifie que la méthode supprime un message lorsque le message est présent dans la base de données. L’autre méthode teste que la base `Id` de données ne change pas si le message de suppression n’existe pas. La `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` méthode est indiquée ci-dessous :

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Tout d’abord, la méthode effectue l’étape Arrange, où la préparation de l’étape de la Loi a lieu. Les messages d’ensemencement sont obtenus et conservés dans `seedMessages`. Les messages d’ensemencement sont enregistrés dans la base de données. Le message `Id` avec `1` un de est réglé pour la suppression. Lorsque `DeleteMessageAsync` la méthode est exécutée, les messages attendus doivent avoir `Id` tous `1`les messages, sauf pour celui avec un de . La `expectedMessages` variable représente ce résultat attendu.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

La méthode agit: La `DeleteMessageAsync` méthode est `recId` `1`exécutée en passant dans le de :

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Enfin, la méthode `Messages` obtient le du contexte et `expectedMessages` la compare à l’affirmation que les deux sont égaux:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Afin de comparer que `List<Message>` les deux sont les mêmes:

* Les messages sont `Id`commandés par .
* Les paires de `Text` messages sont comparées sur la propriété.

Une méthode de `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` test similaire, vérifie le résultat de la tentative de supprimer un message qui n’existe pas. Dans ce cas, les messages attendus dans la base `DeleteMessageAsync` de données doivent être égaux aux messages réels après l’exécution de la méthode. Il ne devrait pas y avoir de modification au contenu de la base de données :

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Tests unitaires des méthodes du modèle de page

Un autre ensemble de tests unitaires est responsable des tests des méthodes du modèle de page. Dans l’application de message, les `IndexModel` modèles de page Index se trouvent dans la classe dans *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Méthode de modèle de page | Fonction |
| ----------------- | -------- |
| `OnGetAsync` | Obtient les messages du DAL pour l’interface utilisateur en utilisant la `GetMessagesAsync` méthode. |
| `OnPostAddMessageAsync` | Si le [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) est `AddMessageAsync` valide, appelle pour ajouter un message à la base de données. |
| `OnPostDeleteAllMessagesAsync` | Appels `DeleteAllMessagesAsync` pour supprimer tous les messages dans la base de données. |
| `OnPostDeleteMessageAsync` | Exécute `DeleteMessageAsync` pour supprimer un `Id` message avec le spécifié. |
| `OnPostAnalyzeMessagesAsync` | Si un ou plusieurs messages sont dans la base de données, calcule le nombre moyen de mots par message. |

Les méthodes du modèle de page `IndexPageTests` sont testées à l’aide de sept tests dans la classe *(tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). Les tests utilisent le modèle familier Arrange-Assert-Act. Ces tests portent sur :

* Déterminer si les méthodes suivent le comportement correct lorsque le [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) est invalide.
* La confirmation des <xref:Microsoft.AspNetCore.Mvc.IActionResult>méthodes produisent le correct .
* Vérifier que les affectations de valeur de propriété sont faites correctement.

Ce groupe de tests se moque souvent des méthodes du DAL pour produire les données attendues pour l’étape de la Loi où une méthode de modèle de page est exécutée. Par exemple, `GetMessagesAsync` la `AppDbContext` méthode de la est moquée pour produire la sortie. Lorsqu’une méthode de modèle de page exécute cette méthode, le simulacre renvoie le résultat. Les données ne proviennent pas de la base de données. Cela crée des conditions de test prévisibles et fiables pour l’utilisation du DAL dans les tests de modèle de page.

Le `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test montre `GetMessagesAsync` comment la méthode est moquée pour le modèle de page:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Lorsque `OnGetAsync` la méthode est exécutée dans l’étape de `GetMessagesAsync` la Loi, elle appelle la méthode du modèle de page.

Étape de la Loi sur les tests*unitaires (tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*) :

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`méthode du `OnGetAsync` modèle de page (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

La `GetMessagesAsync` méthode dans le DAL ne renvoie pas le résultat pour cet appel de méthode. La version simulée de la méthode renvoie le résultat.

Dans `Assert` l’étape, les`actualMessages`messages réels `Messages` ( ) sont attribués à partir de la propriété du modèle de page. Une vérification de type est également effectuée lorsque les messages sont assignés. Les messages attendus et réels `Text` sont comparés par leurs propriétés. Le test affirme que `List<Message>` les deux instances contiennent les mêmes messages.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

D’autres tests dans ce groupe <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>créer <xref:Microsoft.AspNetCore.Mvc.ActionContext> des objets `PageContext`modèle `ViewDataDictionary`de `PageContext`page qui comprennent le , le , un pour établir le , a , et un . Ceux-ci sont utiles pour effectuer des tests. Par exemple, l’application `ModelState` de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> message établit une <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> erreur `OnPostAddMessageAsync` pour vérifier qu’un valide est retourné lors de l’exécution :

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Effectuer des tests unitaires de C# dans .NET Core à l’aide de dotnet test et de xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Test unitaire votre code](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Génération d’une solution .NET Core complète sur macOS à l’aide de Visual Studio pour Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Commencer avec xUnit.net: Utilisation de .NET Core avec la ligne de commande .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
