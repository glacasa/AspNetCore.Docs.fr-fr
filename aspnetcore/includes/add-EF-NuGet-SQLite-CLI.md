<span data-ttu-id="10ba9-101">Exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="10ba9-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

<span data-ttu-id="10ba9-102">Les commandes précédentes ajoutent :</span><span class="sxs-lookup"><span data-stu-id="10ba9-102">The preceding commands add:</span></span>

* <span data-ttu-id="10ba9-103">L' [outil de génération de modèles automatique ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="10ba9-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="10ba9-104">Les outils de Entity Framework Core pour le CLI .NET Core.</span><span class="sxs-lookup"><span data-stu-id="10ba9-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="10ba9-105">Le fournisseur EF Core SQLite, qui installe le package EF Core comme une dépendance.</span><span class="sxs-lookup"><span data-stu-id="10ba9-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="10ba9-106">Les packages nécessaires à la génération de modèles automatique : `Microsoft.VisualStudio.Web.CodeGeneration.Design` et `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="10ba9-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="10ba9-107">Pour obtenir des conseils sur la configuration de plusieurs environnements qui permet à une application de configurer ses contextes de base de données par environnement, consultez <xref:fundamentals/environments#environment-based-startup-class-and-methods> .</span><span class="sxs-lookup"><span data-stu-id="10ba9-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]