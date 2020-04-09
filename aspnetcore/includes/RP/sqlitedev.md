### <a name="use-sqlite-for-development-sql-server-for-production"></a>Utiliser SQLite pour le développement, SQL Server pour la production

Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement. Le code suivant montre <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> comment injecter dans Startup. `IWebHostEnvironment`est injecté `ConfigureServices` ainsi peut utiliser SQLite dans le développement et SQL Server en production.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]
