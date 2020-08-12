<a name="csc"></a>

## <a name="combining-service-collection"></a>Combinaison de la collection de services

Tenez compte des éléments suivants `ConfigureServices` qui contiennent plusieurs collections de services :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Les groupes d’inscriptions associés peuvent être déplacés vers une méthode d’extension pour inscrire les services. Par exemple, les services de configuration sont ajoutés à la classe suivante :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

Les services restants sont inscrits dans une classe similaire. L’exemple suivant `ConfigureServices` utilise les nouvelles méthodes d’extension pour inscrire les services :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***Remarque :*** Chaque `services.Add{SERVICE_NAME}` méthode d’extension ajoute et configure éventuellement des services. Par exemple, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> ajoute les services MVC dont les vues ont besoin. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>Ajoute les services requis Razor Pages. Nous vous recommandons d’appliquer cette Convention d’affectation de noms aux applications. Placez les méthodes d’extension dans l’espace de noms [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pour encapsuler des groupes d’inscriptions de service.