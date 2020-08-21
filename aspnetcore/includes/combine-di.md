<a name="csc"></a>

Considérez la `ConfigureServices` méthode suivante, qui inscrit les services et configure les options :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Les groupes d’inscriptions associés peuvent être déplacés vers une méthode d’extension pour inscrire les services. Par exemple, les services de configuration sont ajoutés à la classe suivante :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

Les services restants sont inscrits dans une classe similaire. La `ConfigureServices` méthode suivante utilise les nouvelles méthodes d’extension pour inscrire les services :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_Remarque :_** Chaque `services.Add{GROUP_NAME}` méthode d’extension ajoute et configure éventuellement des services. Par exemple, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> ajoute les services de contrôleurs MVC requis et <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> ajoute les services requis Razor pages. Nous vous recommandons d’appliquer cette Convention d’affectation de noms aux applications. Placez les méthodes d’extension dans l’espace de noms <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> pour encapsuler des groupes d’inscriptions de service.
