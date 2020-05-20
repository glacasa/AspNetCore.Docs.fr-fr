---
titre : « créer votre première Blazor application » auteur : Description : « créer une Blazor application pas à pas ».
monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

---
# <a name="build-your-first-blazor-app"></a>Créer votre première Blazor application

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

Ce didacticiel vous montre comment créer et modifier une Blazor application. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet d’application de liste de tâches Blazor
> * Modifier les Razor composants
> * Utiliser la gestion des événements et la liaison de données dans les composants
> * Utiliser l’injection de dépendances et le routage dans une Blazor application

À la fin de ce didacticiel, vous disposerez d’une application de conversation de travail.

## <a name="build-components"></a>Construire des composants

1. Suivez les instructions de l' <xref:blazor/get-started> article pour créer un Blazor projet pour ce didacticiel. Nommez le projet *ToDoList*.

1. Accédez à chacune des trois pages de l’application dans le dossier *pages* : Hébergement, compteur et extraction de données. Ces pages sont implémentées par les Razor fichiers de composant *index. Razor*, *Counter. Razor*et *fetchData. Razor*.

1. Sur la page Counter, sélectionnez le bouton **Click me** pour incrémenter le compteur sans actualisation de la page. L’incrémentation d’un compteur dans une page Web nécessite normalement l’écriture de code JavaScript. Avec Blazor , vous pouvez écrire en C# à la place.

1. Examinez l’implémentation du composant `Counter` dans le fichier *Counter.razor*.

   *Pages/Counter.razor* :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   L’interface utilisateur du composant `Counter` est définie à l’aide de HTML. La logique de rendu dynamique (par exemple, les boucles, les instructions conditionnelles, les expressions) est ajoutée à l’aide d’une syntaxe C# incorporée appelée [Razor](xref:mvc/views/razor) . Le balisage HTML et la logique de rendu C# sont convertis en une classe de composants au moment de la génération. Le nom de la classe .NET générée correspond à celui du fichier.

   Les membres de la classe de composants sont définis dans un bloc `@code`. Dans le bloc `@code`, l’état du composant (propriétés, champs) et les méthodes sont spécifiés pour la gestion des événements ou pour définir une autre logique de composant. Ces membres sont ensuite utilisés dans le cadre de la logique de rendu du composant et la gestion des événements.

   Lorsque le bouton **Click me** est sélectionné :

   * Le gestionnaire `onclick` enregistré du composant `Counter` est appelé (méthode `IncrementCount`).
   * Le composant `Counter` regénère son arborescence de rendu.
   * La nouvelle arborescence de rendu est comparée à la précédente.
   * Seules les modifications apportées à Document Object Model (DOM) sont appliquées. Le compte affiché est mis à jour.

1. Modifiez la logique C# du composant `Counter` pour que l’incrément du compte passe de un à deux.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Régénérez et exécutez l’application pour voir les modifications. Sélectionnez le bouton **Click me**. Le compteur est incrémenté de deux.

## <a name="use-components"></a>Utiliser des composants

Incluez un composant dans un autre composant utilisant une syntaxe HTML.

1. Ajoutez le composant `Counter` au composant `Index` de l’application en ajoutant un élément `<Counter />` au composant `Index` (*Index.razor*).

   Si vous utilisez Blazor Webassembly pour cette expérience, un `SurveyPrompt` composant est utilisé par le `Index` composant. Remplacez l’élément `<SurveyPrompt>` par un élément `<Counter />`. Si vous utilisez une Blazor application serveur pour cette expérience, ajoutez l' `<Counter />` élément au `Index` composant :

   *Pages/Index.razor* :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Régénérez et exécutez l'application. Le composant `Index` a son propre compteur.

## <a name="component-parameters"></a>Paramètres de composant

Les composants peuvent également avoir des paramètres. Les paramètres de composant sont définis à l’aide de propriétés publiques sur la classe de composant avec l' `[Parameter]` attribut. Utilisez des attributs pour spécifier des arguments pour un composant dans le balisage.

1. Mettez à jour le `@code` code C# du composant comme suit :

   * Ajoutez une `IncrementAmount` propriété publique avec l' `[Parameter]` attribut.
   * Modifiez la `IncrementCount` méthode pour utiliser la `IncrementAmount` propriété lors de l’incrémentation de la valeur de `currentCount` .

   *Pages/Counter.razor* :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Spécifiez un paramètre `IncrementAmount` dans l’élément `<Counter>` du composant `Index` à l’aide d’un attribut. Définissez la valeur pour incrémenter le compteur par 10.

   *Pages/Index.razor* :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Rechargez le composant `Index`. Le compteur s’incrémente de dix unités chaque fois que le bouton **Cliquer ici** est sélectionné. Le compteur dans le composant `Counter` continue à être incrémenté d’un.

## <a name="route-to-components"></a>Acheminer vers les composants

La directive `@page` en haut du fichier *Counter.razor* spécifie que le composant `Counter` est un point de terminaison de routage. Le composant `Counter` gère les requêtes envoyées à `/counter`. Sans la directive `@page`, un composant ne gère pas les requêtes acheminées, mais le composant peut toujours être utilisé par d’autres composants.

## <a name="dependency-injection"></a>Injection de dépendances

### <a name="blazor-server-experience"></a>BlazorExpérience du serveur

Si vous utilisez une Blazor application serveur, le `WeatherForecastService` service est inscrit en tant que [Singleton](xref:fundamentals/dependency-injection#service-lifetimes) dans `Startup.ConfigureServices` . Une instance du service est disponible dans l’ensemble de l’application via l' [injection de dépendances (di)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

La `@inject` directive est utilisée pour injecter l’instance du `WeatherForecastService` service dans le `FetchData` composant.

*Pages/FetchData.razor* :

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Le composant `FetchData` utilise le service injecté, comme `ForecastService`, pour récupérer un tableau d’objets `WeatherForecast` :

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>BlazorExpérience webassembly

Si vous utilisez une Blazor application Webassembly, `HttpClient` est injecté pour obtenir des données de prévision météorologiques à partir du fichier *Weather. JSON* dans le dossier *wwwroot/Sample-Data* .

*Pages/FetchData.razor* :

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

Une [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) boucle est utilisée pour afficher chaque instance de prévision sous la forme d’une ligne dans la table des données météorologiques :

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Générer une liste de tâches

Ajoutez un nouveau composant à l’application qui implémente une liste de tâches simple.

1. Ajoutez un nouveau `Todo` Razor composant à l’application dans le dossier *pages* . Dans Visual Studio, cliquez avec le bouton droit sur le dossier **pages** , puis sélectionnez **Ajouter**  >  **un nouvel élément**  >  ** Razor composant**. Nommez le fichier *TODO. Razor*du composant. Dans d’autres environnements de développement, ajoutez un fichier vide au dossier **pages** nommé *TODO. Razor*.

1. Fournissez le balisage initial pour le composant :

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Ajoutez le composant `Todo` à la barre de navigation.

   Le composant `NavMenu` (*Shared/NavMenu.razor*) est utilisé dans la disposition de l’application. Les dispositions sont des composants qui vous permettent d’éviter la duplication de contenu dans l’application.

   Ajoutez un élément `<NavLink>` pour le composant `Todo` en ajoutant le balisage d’élément de liste suivant sous les éléments de liste existants dans le fichier *Shared/NavMenu.razor* :

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Régénérez et exécutez l'application. Consultez la nouvelle page Todo pour vérifier que le lien vers le composant `Todo` fonctionne.

1. Ajoutez un fichier *TodoItem.cs* à la racine du projet pour contenir une classe qui représente un élément Todo. Utilisez le code C# suivant pour la classe `TodoItem` :

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Revenez au composant `Todo` (*Pages/Todo.razorl*) :

   * Ajoutez un champ pour les éléments Todo dans un bloc `@code`. Le composant `Todo` utilise ce champ pour maintenir l’état de la liste de tâches.
   * Ajoutez un balisage de liste non triée et une boucle `foreach` pour effectuer le rendu de chaque élément todo en tant qu’élément de liste (`<li>`).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. L’application nécessite des éléments d’interface utilisateur pour ajouter des éléments todo à la liste. Ajoutez une entrée de texte (`<input>`) et un bouton (`<button>`) sous la liste non ordonnée (`<ul>...</ul>`) :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Régénérez et exécutez l'application. Lorsque le bouton **Add todo** est sélectionné, rien ne se produit car aucun gestionnaire d’événements n’est lié au bouton.

1. Ajoutez une méthode `AddTodo` au composant `Todo` et enregistrez-la pour les sélections du bouton à l’aide de l’attribut `@onclick`. La méthode C# `AddTodo` est appelée lorsque le bouton est sélectionné :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Pour obtenir le titre du nouvel élément todo, ajoutez un champ de chaîne `newTodo` en haut du bloc `@code` et liez-le à la valeur du texte d’entrée à l’aide de l’attribut `bind` dans l’élément `<input>` :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Mettez à jour la méthode `AddTodo` pour ajouter `TodoItem` avec le titre spécifié à la liste. Supprimez la valeur du texte d’entrée en définissant `newTodo` sur une chaîne vide :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Régénérez et exécutez l'application. Ajoutez quelques éléments todo à la liste Todo pour tester le nouveau code.

1. Le texte du titre pour chaque élément todo peut être rendu modifiable et une case à cocher peut aider l’utilisateur à effectuer le suivi des éléments terminés. Ajoutez une entrée de case à cocher pour chaque élément todo et liez sa valeur à la propriété `IsDone`. Remplacez `@todo.Title` par un élément `<input>` lié à `@todo.Title` :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Pour vérifier que ces valeurs sont liées, mettez à jour l’en-tête `<h3>` pour afficher le nombre d’éléments todo qui ne sont pas terminés (`IsDone` est `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Le composant `Todo` (*Pages/Todo.razor*) terminé :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Régénérez et exécutez l'application. Ajoutez des éléments todo pour tester le nouveau code.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un projet d’application de liste de tâches Blazor
> * Modifier les Razor composants
> * Utiliser la gestion des événements et la liaison de données dans les composants
> * Utiliser l’injection de dépendances et le routage dans une Blazor application

Découvrez comment créer et utiliser des composants :

> [!div class="nextstepaction"]
> <xref:blazor/components>
