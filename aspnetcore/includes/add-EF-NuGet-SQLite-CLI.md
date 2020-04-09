Exécutez les commandes CLI .NET Core suivantes :

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

Les commandes précédentes ajoutent :

* [L’outil d’échafaudage aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* Les outils de base du cadre d’entité pour l’ICI CLI de base .NET.
* Le fournisseur EF Core SQLite, qui installe le package EF Core comme une dépendance.
* Les packages nécessaires à la génération de modèles automatique : `Microsoft.VisualStudio.Web.CodeGeneration.Design` et `Microsoft.EntityFrameworkCore.SqlServer`.

Pour obtenir des conseils sur la configuration de l’environnement <xref:fundamentals/environments#environment-based-startup-class-and-methods>multiple qui permet à une application de configurer ses contextes de base de données par environnement, voir .

[!INCLUDE[](~/includes/scaffoldTFM.md)]