---
title: Créer une Blazor application de liste de tâches
author: guardrex
description: Générez une Blazor application pas à pas.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 7335b68ad06b9d2b8d7e056cfc1a6d8214119b21
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865413"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="fe431-103">Créer une Blazor application de liste de tâches</span><span class="sxs-lookup"><span data-stu-id="fe431-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="fe431-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fe431-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fe431-105">Ce didacticiel vous montre comment créer et modifier une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="fe431-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="fe431-106">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="fe431-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe431-107">Créer un projet d’application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="fe431-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="fe431-108">Modifier les Razor composants</span><span class="sxs-lookup"><span data-stu-id="fe431-108">Modify Razor components</span></span>
> * <span data-ttu-id="fe431-109">Utiliser la gestion des événements et la liaison de données dans les composants</span><span class="sxs-lookup"><span data-stu-id="fe431-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="fe431-110">Utiliser le routage dans une Blazor application</span><span class="sxs-lookup"><span data-stu-id="fe431-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="fe431-111">À la fin de ce didacticiel, vous disposerez d’une application de liste de tâches en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="fe431-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fe431-112">Prérequis</span><span class="sxs-lookup"><span data-stu-id="fe431-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="fe431-113">Créer une application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="fe431-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="fe431-114">Créez une Blazor application nommée `TodoList` dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="fe431-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="fe431-115">La commande précédente crée un dossier nommé `TodoList` pour contenir l’application.</span><span class="sxs-lookup"><span data-stu-id="fe431-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="fe431-116">Le `TodoList` dossier est le *dossier racine* du projet.</span><span class="sxs-lookup"><span data-stu-id="fe431-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="fe431-117">Accédez au dossier à l' `TodoList` aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="fe431-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="fe431-118">Ajoutez un nouveau `Todo` Razor composant à l’application dans le `Pages` dossier à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="fe431-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="fe431-119">Razor les noms de fichiers de composant requièrent une première lettre capitalisée.</span><span class="sxs-lookup"><span data-stu-id="fe431-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="fe431-120">Ouvrez le `Pages` dossier et vérifiez que le `Todo` nom de fichier du composant commence par une lettre majuscule `T` .</span><span class="sxs-lookup"><span data-stu-id="fe431-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="fe431-121">Le nom de fichier doit être `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="fe431-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="fe431-122">Dans `Pages/Todo.razor` fournir le balisage initial pour le composant :</span><span class="sxs-lookup"><span data-stu-id="fe431-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="fe431-123">Ajoutez le composant `Todo` à la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="fe431-123">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="fe431-124">Le `NavMenu` composant ( `Shared/NavMenu.razor` ) est utilisé dans la disposition de l’application.</span><span class="sxs-lookup"><span data-stu-id="fe431-124">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="fe431-125">Les dispositions sont des composants qui vous permettent d’éviter la duplication de contenu dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fe431-125">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="fe431-126">Ajoutez un `<NavLink>` élément pour le `Todo` composant en ajoutant la balise d’élément de liste suivante sous les éléments de liste existants dans le `Shared/NavMenu.razor` fichier :</span><span class="sxs-lookup"><span data-stu-id="fe431-126">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="fe431-127">Générez et exécutez l’application en exécutant la `dotnet run` commande dans l’interface de commande à partir du `TodoList` dossier.</span><span class="sxs-lookup"><span data-stu-id="fe431-127">Build and run the app by executing the `dotnet run` command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="fe431-128">Consultez la nouvelle page Todo pour vérifier que le lien vers le composant `Todo` fonctionne.</span><span class="sxs-lookup"><span data-stu-id="fe431-128">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="fe431-129">Ajoutez un `TodoItem.cs` fichier à la racine du projet (le `TodoList` dossier) pour contenir une classe qui représente un élément TODO.</span><span class="sxs-lookup"><span data-stu-id="fe431-129">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="fe431-130">Utilisez le code C# suivant pour la classe `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="fe431-130">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="fe431-131">Revenez au `Todo` composant ( `Pages/Todo.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="fe431-131">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="fe431-132">Ajoutez un champ pour les éléments Todo dans un bloc `@code`.</span><span class="sxs-lookup"><span data-stu-id="fe431-132">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="fe431-133">Le composant `Todo` utilise ce champ pour maintenir l’état de la liste de tâches.</span><span class="sxs-lookup"><span data-stu-id="fe431-133">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="fe431-134">Ajoutez un balisage de liste non triée et une boucle `foreach` pour effectuer le rendu de chaque élément todo en tant qu’élément de liste (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="fe431-134">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="fe431-135">L’application nécessite des éléments d’interface utilisateur pour ajouter des éléments todo à la liste.</span><span class="sxs-lookup"><span data-stu-id="fe431-135">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="fe431-136">Ajoutez une entrée de texte (`<input>`) et un bouton (`<button>`) sous la liste non ordonnée (`<ul>...</ul>`) :</span><span class="sxs-lookup"><span data-stu-id="fe431-136">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="fe431-137">Arrêtez l’application en cours d’exécution dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="fe431-137">Stop the running app in the command shell.</span></span> <span data-ttu-id="fe431-138">De nombreux interpréteurs de commandes acceptent la commande de clavier <kbd>CTRL</kbd> + <kbd>c</kbd> pour arrêter une application.</span><span class="sxs-lookup"><span data-stu-id="fe431-138">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span> <span data-ttu-id="fe431-139">Régénérez et exécutez l’application avec la `dotnet run` commande.</span><span class="sxs-lookup"><span data-stu-id="fe431-139">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="fe431-140">Lorsque le **`Add todo`** bouton est sélectionné, rien ne se produit, car un gestionnaire d’événements n’est pas relié au bouton.</span><span class="sxs-lookup"><span data-stu-id="fe431-140">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="fe431-141">Ajoutez une méthode `AddTodo` au composant `Todo` et enregistrez-la pour les sélections du bouton à l’aide de l’attribut `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="fe431-141">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="fe431-142">La méthode C# `AddTodo` est appelée lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="fe431-142">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="fe431-143">Pour obtenir le titre du nouvel élément todo, ajoutez un champ de chaîne `newTodo` en haut du bloc `@code` et liez-le à la valeur du texte d’entrée à l’aide de l’attribut `bind` dans l’élément `<input>` :</span><span class="sxs-lookup"><span data-stu-id="fe431-143">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="fe431-144">Mettez à jour la méthode `AddTodo` pour ajouter `TodoItem` avec le titre spécifié à la liste.</span><span class="sxs-lookup"><span data-stu-id="fe431-144">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="fe431-145">Supprimez la valeur du texte d’entrée en définissant `newTodo` sur une chaîne vide :</span><span class="sxs-lookup"><span data-stu-id="fe431-145">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="fe431-146">Arrêtez l’application en cours d’exécution dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="fe431-146">Stop the running app in the command shell.</span></span> <span data-ttu-id="fe431-147">Régénérez et exécutez l’application avec la `dotnet run` commande.</span><span class="sxs-lookup"><span data-stu-id="fe431-147">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="fe431-148">Ajoutez quelques éléments todo à la liste Todo pour tester le nouveau code.</span><span class="sxs-lookup"><span data-stu-id="fe431-148">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="fe431-149">Le texte du titre pour chaque élément todo peut être rendu modifiable et une case à cocher peut aider l’utilisateur à effectuer le suivi des éléments terminés.</span><span class="sxs-lookup"><span data-stu-id="fe431-149">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="fe431-150">Ajoutez une entrée de case à cocher pour chaque élément todo et liez sa valeur à la propriété `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="fe431-150">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="fe431-151">Remplacez `@todo.Title` par un élément `<input>` lié à `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="fe431-151">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="fe431-152">Pour vérifier que ces valeurs sont liées, mettez à jour l’en-tête `<h3>` pour afficher le nombre d’éléments todo qui ne sont pas terminés (`IsDone` est `false`).</span><span class="sxs-lookup"><span data-stu-id="fe431-152">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="fe431-153">Le `Todo` composant terminé ( `Pages/Todo.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="fe431-153">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="fe431-154">Arrêtez l’application en cours d’exécution dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="fe431-154">Stop the running app in the command shell.</span></span> <span data-ttu-id="fe431-155">Régénérez et exécutez l’application avec la `dotnet run` commande.</span><span class="sxs-lookup"><span data-stu-id="fe431-155">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="fe431-156">Ajoutez des éléments todo pour tester le nouveau code.</span><span class="sxs-lookup"><span data-stu-id="fe431-156">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fe431-157">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="fe431-157">Next steps</span></span>

<span data-ttu-id="fe431-158">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="fe431-158">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe431-159">Créer un projet d’application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="fe431-159">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="fe431-160">Modifier les Razor composants</span><span class="sxs-lookup"><span data-stu-id="fe431-160">Modify Razor components</span></span>
> * <span data-ttu-id="fe431-161">Utiliser la gestion des événements et la liaison de données dans les composants</span><span class="sxs-lookup"><span data-stu-id="fe431-161">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="fe431-162">Utiliser le routage dans une Blazor application</span><span class="sxs-lookup"><span data-stu-id="fe431-162">Use routing in a Blazor app</span></span>

<span data-ttu-id="fe431-163">En savoir plus sur les outils pour ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="fe431-163">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
