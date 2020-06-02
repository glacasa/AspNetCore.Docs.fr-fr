---
<span data-ttu-id="0317a-101">title : migrer de Microsoft. extensions. Logging 2,1 vers 2,2 ou 3,0 Author : pakrym Description : Découvrez comment migrer une application non-ASP.NET Core qui utilise Microsoft. extensions. Logging de 2,1 à 2,2 ou 3,0.</span><span class="sxs-lookup"><span data-stu-id="0317a-101">title: Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0 author: pakrym description: Learn how to migrate a non-ASP.NET Core application that uses Microsoft.Extensions.Logging from 2.1 to 2.2 or 3.0.</span></span>
<span data-ttu-id="0317a-102">ms. Author : pakrym ms. Custom : MVC ms. Date : 01/04/2019 No-Loc :</span><span class="sxs-lookup"><span data-stu-id="0317a-102">ms.author: pakrym ms.custom: mvc ms.date: 01/04/2019 no-loc:</span></span>
- <span data-ttu-id="0317a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0317a-103">'Blazor'</span></span>
- <span data-ttu-id="0317a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0317a-104">'Identity'</span></span>
- <span data-ttu-id="0317a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0317a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="0317a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0317a-106">'Razor'</span></span>
- <span data-ttu-id="0317a-107">' SignalR 'UID : migration/journalisation-nonaspnetcore</span><span class="sxs-lookup"><span data-stu-id="0317a-107">'SignalR' uid: migration/logging-nonaspnetcore</span></span>

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="0317a-108">Migrer de Microsoft. extensions. Logging 2,1 vers 2,2 ou 3,0</span><span class="sxs-lookup"><span data-stu-id="0317a-108">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="0317a-109">Cet article décrit les étapes courantes de la migration d’une application non-ASP.NET Core qui utilise `Microsoft.Extensions.Logging` de 2,1 à 2,2 ou 3,0.</span><span class="sxs-lookup"><span data-stu-id="0317a-109">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="0317a-110">2.1 à 2.2</span><span class="sxs-lookup"><span data-stu-id="0317a-110">2.1 to 2.2</span></span>

<span data-ttu-id="0317a-111">Créez `ServiceCollection` et appelez manuellement `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="0317a-111">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="0317a-112">2,1 exemple :</span><span class="sxs-lookup"><span data-stu-id="0317a-112">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="0317a-113">2,2 exemple :</span><span class="sxs-lookup"><span data-stu-id="0317a-113">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="0317a-114">2,1 à 3,0</span><span class="sxs-lookup"><span data-stu-id="0317a-114">2.1 to 3.0</span></span>

<span data-ttu-id="0317a-115">Dans 3,0, utilisez `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="0317a-115">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="0317a-116">2,1 exemple :</span><span class="sxs-lookup"><span data-stu-id="0317a-116">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="0317a-117">3,0 exemple :</span><span class="sxs-lookup"><span data-stu-id="0317a-117">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="0317a-118">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0317a-118">Additional resources</span></span>

* <span data-ttu-id="0317a-119">[Package NuGet Microsoft. extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="0317a-119">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
