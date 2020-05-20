---
<span data-ttu-id="625eb-101">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-101">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-103">'Blazor'</span></span>
- <span data-ttu-id="625eb-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-104">'Identity'</span></span>
- <span data-ttu-id="625eb-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-106">'Razor'</span></span>
- <span data-ttu-id="625eb-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-107">'SignalR' uid:</span></span> 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="625eb-108">Configurer l’éditeur de liens pour ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="625eb-108">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="625eb-109">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="625eb-109">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="625eb-110">Webassembly effectue une liaison [il (Intermediate Language)](/dotnet/standard/managed-code#intermediate-language--execution) au cours d’une génération pour supprimer l’il inutile des assemblys de sortie de l’application.</span><span class="sxs-lookup"><span data-stu-id="625eb-110"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="625eb-111">L’éditeur de liens est désactivé lors de la génération dans la configuration Debug.</span><span class="sxs-lookup"><span data-stu-id="625eb-111">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="625eb-112">Les applications doivent être générées dans la configuration Release pour activer l’éditeur de liens.</span><span class="sxs-lookup"><span data-stu-id="625eb-112">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="625eb-113">Nous vous recommandons de créer la version finale lors du déploiement de vos Blazor applications Webassembly.</span><span class="sxs-lookup"><span data-stu-id="625eb-113">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="625eb-114">La liaison d’une application optimise sa taille, mais peut avoir des effets néfastes.</span><span class="sxs-lookup"><span data-stu-id="625eb-114">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="625eb-115">Les applications qui utilisent la réflexion ou les fonctionnalités dynamiques associées peuvent s’arrêter en cas de troncation, car l’éditeur de liens ne connaît pas ce comportement dynamique et ne peut pas déterminer en général les types requis pour la réflexion au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="625eb-115">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="625eb-116">Pour supprimer de telles applications, l’éditeur de liens doit être informé des types requis par la réflexion dans le code et dans les packages ou infrastructures dont dépend l’application.</span><span class="sxs-lookup"><span data-stu-id="625eb-116">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="625eb-117">Pour garantir le bon fonctionnement de l’application tronquée une fois déployée, il est important de tester fréquemment les versions release de l’application lors du développement.</span><span class="sxs-lookup"><span data-stu-id="625eb-117">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="625eb-118">La liaison des Blazor applications peut être configurée à l’aide de ces fonctionnalités MSBuild :</span><span class="sxs-lookup"><span data-stu-id="625eb-118">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="625eb-119">Configurez la liaison globale avec une [propriété MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="625eb-119">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="625eb-120">Contrôle de liaison de chaque assembly avec un [fichier de configuration](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="625eb-120">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="625eb-121">Liaison de contrôle avec une propriété MSBuild</span><span class="sxs-lookup"><span data-stu-id="625eb-121">Control linking with an MSBuild property</span></span>

<span data-ttu-id="625eb-122">La liaison est activée lorsqu’une application est intégrée à la `Release` Configuration.</span><span class="sxs-lookup"><span data-stu-id="625eb-122">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="625eb-123">Pour modifier cela, configurez la `BlazorWebAssemblyEnableLinking` propriété MSBuild dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="625eb-123">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="625eb-124">Contrôler la liaison avec un fichier de configuration</span><span class="sxs-lookup"><span data-stu-id="625eb-124">Control linking with a configuration file</span></span>

<span data-ttu-id="625eb-125">Contrôlez la liaison pour chaque assembly en fournissant un fichier de configuration XML et en spécifiant le fichier en tant qu’élément MSBuild dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="625eb-125">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="625eb-126">*LinkerConfig. xml*:</span><span class="sxs-lookup"><span data-stu-id="625eb-126">*LinkerConfig.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="625eb-127">Pour plus d’informations et d’exemples, consultez [formats de données (référentiel GitHub mono/éditeur de liens)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="625eb-127">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="625eb-128">Ajouter un fichier de configuration de l’éditeur de liens XML à une bibliothèque</span><span class="sxs-lookup"><span data-stu-id="625eb-128">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="625eb-129">Pour configurer l’éditeur de liens pour une bibliothèque spécifique, ajoutez un fichier de configuration de l’éditeur de liens XML dans la bibliothèque en tant que ressource incorporée.</span><span class="sxs-lookup"><span data-stu-id="625eb-129">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="625eb-130">La ressource incorporée doit avoir le même nom que l’assembly.</span><span class="sxs-lookup"><span data-stu-id="625eb-130">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="625eb-131">Dans l’exemple suivant, le fichier *LinkerConfig. xml* est spécifié en tant que ressource incorporée qui porte le même nom que l’assembly de la bibliothèque :</span><span class="sxs-lookup"><span data-stu-id="625eb-131">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="625eb-132">Configurer l’éditeur de liens pour l’internationalisation</span><span class="sxs-lookup"><span data-stu-id="625eb-132">Configure the linker for internationalization</span></span>

<span data-ttu-id="625eb-133">Par défaut, Blazor la configuration de l’éditeur de liens pour les Blazor applications webassembly supprime les informations d’internationalisation, à l’exception des paramètres régionaux demandés explicitement.</span><span class="sxs-lookup"><span data-stu-id="625eb-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="625eb-134">La suppression de ces assemblys réduit la taille de l’application.</span><span class="sxs-lookup"><span data-stu-id="625eb-134">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="625eb-135">Pour contrôler les assemblys I18N qui sont conservés, définissez la `<BlazorWebAssemblyI18NAssemblies>` propriété MSBuild dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="625eb-135">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="625eb-136">Valeur de la région</span><span class="sxs-lookup"><span data-stu-id="625eb-136">Region Value</span></span>     | <span data-ttu-id="625eb-137">Assembly de région mono</span><span class="sxs-lookup"><span data-stu-id="625eb-137">Mono region assembly</span></span>    |
| ---
<span data-ttu-id="625eb-138">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-138">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-139">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-140">'Blazor'</span></span>
- <span data-ttu-id="625eb-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-141">'Identity'</span></span>
- <span data-ttu-id="625eb-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-143">'Razor'</span></span>
- <span data-ttu-id="625eb-144">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-144">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-145">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-145">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-146">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-147">'Blazor'</span></span>
- <span data-ttu-id="625eb-148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-148">'Identity'</span></span>
- <span data-ttu-id="625eb-149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-149">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-150">'Razor'</span></span>
- <span data-ttu-id="625eb-151">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-151">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-152">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-152">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-153">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-154">'Blazor'</span></span>
- <span data-ttu-id="625eb-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-155">'Identity'</span></span>
- <span data-ttu-id="625eb-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-157">'Razor'</span></span>
- <span data-ttu-id="625eb-158">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-159">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-159">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-160">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-161">'Blazor'</span></span>
- <span data-ttu-id="625eb-162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-162">'Identity'</span></span>
- <span data-ttu-id="625eb-163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-163">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-164">'Razor'</span></span>
- <span data-ttu-id="625eb-165">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-166">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-166">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-167">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-168">'Blazor'</span></span>
- <span data-ttu-id="625eb-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-169">'Identity'</span></span>
- <span data-ttu-id="625eb-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-171">'Razor'</span></span>
- <span data-ttu-id="625eb-172">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-173">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-173">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-174">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-175">'Blazor'</span></span>
- <span data-ttu-id="625eb-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-176">'Identity'</span></span>
- <span data-ttu-id="625eb-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-178">'Razor'</span></span>
- <span data-ttu-id="625eb-179">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-179">'SignalR' uid:</span></span> 

<span data-ttu-id="625eb-180">-------- | ---titre : 'configurer l’éditeur de liens pour ASP.NET Core Blazor 'auteur : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-180">-------- | --- title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-181">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-182">'Blazor'</span></span>
- <span data-ttu-id="625eb-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-183">'Identity'</span></span>
- <span data-ttu-id="625eb-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-185">'Razor'</span></span>
- <span data-ttu-id="625eb-186">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-187">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-187">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-188">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-189">'Blazor'</span></span>
- <span data-ttu-id="625eb-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-190">'Identity'</span></span>
- <span data-ttu-id="625eb-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-192">'Razor'</span></span>
- <span data-ttu-id="625eb-193">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-194">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-194">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-195">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-196">'Blazor'</span></span>
- <span data-ttu-id="625eb-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-197">'Identity'</span></span>
- <span data-ttu-id="625eb-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-199">'Razor'</span></span>
- <span data-ttu-id="625eb-200">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-201">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-201">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-202">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-203">'Blazor'</span></span>
- <span data-ttu-id="625eb-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-204">'Identity'</span></span>
- <span data-ttu-id="625eb-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-206">'Razor'</span></span>
- <span data-ttu-id="625eb-207">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-208">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-208">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-209">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-210">'Blazor'</span></span>
- <span data-ttu-id="625eb-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-211">'Identity'</span></span>
- <span data-ttu-id="625eb-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-213">'Razor'</span></span>
- <span data-ttu-id="625eb-214">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-215">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-215">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-216">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-217">'Blazor'</span></span>
- <span data-ttu-id="625eb-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-218">'Identity'</span></span>
- <span data-ttu-id="625eb-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-220">'Razor'</span></span>
- <span data-ttu-id="625eb-221">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-222">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-222">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-223">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-224">'Blazor'</span></span>
- <span data-ttu-id="625eb-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-225">'Identity'</span></span>
- <span data-ttu-id="625eb-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-227">'Razor'</span></span>
- <span data-ttu-id="625eb-228">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-229">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-229">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-230">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-231">'Blazor'</span></span>
- <span data-ttu-id="625eb-232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-232">'Identity'</span></span>
- <span data-ttu-id="625eb-233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-233">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-234">'Razor'</span></span>
- <span data-ttu-id="625eb-235">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="625eb-236">title : 'Configure the Linker for ASP.NET Core Blazor 'Author : Description : 'Apprenez à contrôler l’éditeur de liens en langage intermédiaire (il) lors de la génération d’une Blazor application. '</span><span class="sxs-lookup"><span data-stu-id="625eb-236">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="625eb-237">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="625eb-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="625eb-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="625eb-238">'Blazor'</span></span>
- <span data-ttu-id="625eb-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="625eb-239">'Identity'</span></span>
- <span data-ttu-id="625eb-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="625eb-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="625eb-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="625eb-241">'Razor'</span></span>
- <span data-ttu-id="625eb-242">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="625eb-242">'SignalR' uid:</span></span> 

<span data-ttu-id="625eb-243">------------ | | `all`            | Tous les assemblys inclus | | `cjk`             |  *I18n. CJK. dll* | | `mideast`         |  *I18n. MidEast. dll* | | `none`(par défaut) | Aucun | | `other`           |  *I18n. Other. dll* | | `rare`            |  *I18n. Rare. dll* | | `west`            |  *I18n. West. dll*         |</span><span class="sxs-lookup"><span data-stu-id="625eb-243">------------ | | `all`            | All assemblies included | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (default) | None                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span></span>

<span data-ttu-id="625eb-244">Utilisez une virgule pour séparer plusieurs valeurs (par exemple, `mideast,west` ).</span><span class="sxs-lookup"><span data-stu-id="625eb-244">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="625eb-245">Pour plus d’informations, consultez [i18n : Pnetlib internationalisation Framework Library (référentiel mono/mono GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="625eb-245">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="625eb-246">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="625eb-246">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
