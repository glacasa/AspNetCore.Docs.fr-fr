---
title: Créer un serveur et un client gRPC .NET Core dans ASP.NET Core
author: juntaoluo
description: Ce tutoriel montre comment créer un service gRPC et un client gRPC sur ASP.NET Core. Découvrez comment créer un projet de service gRPC, modifier un fichier proto et ajouter un appel duplex de streaming.
ms.author: johluo
ms.date: 04/08/2020
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 2bbd40b4b89af170dae40b8a5277749d6bcd5faf
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994628"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="7d87c-104">Tutorial: Créer un client et un serveur gRPC dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d87c-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="7d87c-105">Par [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="7d87c-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="7d87c-106">Ce tutoriel montre comment créer un client [gRPC](https://grpc.io/docs/guides/) .NET Core et un serveur gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7d87c-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="7d87c-107">À la fin, vous disposerez d’un client gRPC qui communique avec le service Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="7d87c-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="7d87c-108">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7d87c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="7d87c-109">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="7d87c-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7d87c-110">Créer un serveur gRPC.</span><span class="sxs-lookup"><span data-stu-id="7d87c-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="7d87c-111">Créez un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="7d87c-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="7d87c-112">Tester le service du client gRPC avec le service Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="7d87c-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7d87c-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="7d87c-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d87c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d87c-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d87c-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d87c-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d87c-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="7d87c-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="7d87c-117">Créer un service gRPC</span><span class="sxs-lookup"><span data-stu-id="7d87c-117">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d87c-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d87c-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7d87c-119">Démarrez Visual Studio et sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="7d87c-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="7d87c-120">Vous pouvez également, dans le menu **Fichier** de Visual Studio, sélectionner **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="7d87c-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7d87c-121">Dans le cadre de la **création d’un nouveau dialogue de projet,** sélectionnez **le service gRPC** et sélectionnez **Next**:</span><span class="sxs-lookup"><span data-stu-id="7d87c-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Créer un nouveau dialogue de projet](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="7d87c-123">Nommez le projet **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="7d87c-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="7d87c-124">Il est important de nommer le projet *GrpcGreeter* pour que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="7d87c-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="7d87c-125">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="7d87c-125">Select **Create**.</span></span>
* <span data-ttu-id="7d87c-126">Dans la boîte de dialogue **Créer un service gRPC** :</span><span class="sxs-lookup"><span data-stu-id="7d87c-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="7d87c-127">Le modèle **Service gRPC** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="7d87c-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="7d87c-128">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="7d87c-128">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d87c-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d87c-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7d87c-130">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="7d87c-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7d87c-131">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="7d87c-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="7d87c-132">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="7d87c-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="7d87c-133">La commande `dotnet new` crée un nouveau service gRPC dans le dossier *GrpcGreeter*.</span><span class="sxs-lookup"><span data-stu-id="7d87c-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="7d87c-134">La commande `code` ouvre le dossier *GrpcGreeter* dans une nouvelle instance de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7d87c-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="7d87c-135">Une boîte de dialogue apparaît avec **les actifs nécessaires à construire et débocher sont absents de 'GrpcGreeter'. Les ajouter ?**</span><span class="sxs-lookup"><span data-stu-id="7d87c-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="7d87c-136">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="7d87c-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d87c-137">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="7d87c-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7d87c-138">À partir d’un terminal, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="7d87c-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="7d87c-139">Les commandes précédentes utilisent le [CLI .NET Core](/dotnet/core/tools/dotnet) pour créer un service gRPC.</span><span class="sxs-lookup"><span data-stu-id="7d87c-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="7d87c-140">Ouvrir le projet</span><span class="sxs-lookup"><span data-stu-id="7d87c-140">Open the project</span></span>

<span data-ttu-id="7d87c-141">De Visual Studio, sélectionnez **File** > **Open,** puis sélectionnez le fichier *GrpcGreeter.csproj.*</span><span class="sxs-lookup"><span data-stu-id="7d87c-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="7d87c-142">Exécuter le service</span><span class="sxs-lookup"><span data-stu-id="7d87c-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="7d87c-143">Les journaux indiquent que le service écoute sur `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="7d87c-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="7d87c-144">Le modèle gRPC est configuré pour utiliser le protocole [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="7d87c-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="7d87c-145">Les clients gRPC doivent utiliser le protocole HTTPS pour appeler le serveur.</span><span class="sxs-lookup"><span data-stu-id="7d87c-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="7d87c-146">MacOS ne prend pas en charge ASP.NET Core gRPC avec TLS.</span><span class="sxs-lookup"><span data-stu-id="7d87c-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="7d87c-147">Une configuration supplémentaire est nécessaire pour exécuter correctement les services gRPC sur MacOS.</span><span class="sxs-lookup"><span data-stu-id="7d87c-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="7d87c-148">Pour plus d’informations, consultez [Impossible de démarrer l’application ASP.NET Core gRPC sur MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="7d87c-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="7d87c-149">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="7d87c-149">Examine the project files</span></span>

<span data-ttu-id="7d87c-150">Fichiers projet *GrpcGreeter* :</span><span class="sxs-lookup"><span data-stu-id="7d87c-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="7d87c-151">*greet.proto* &ndash;Le fichier *Protos/greet.proto* définit gRPC `Greeter` et est utilisé pour générer les ressources du serveur gRPC.</span><span class="sxs-lookup"><span data-stu-id="7d87c-151">*greet.proto* &ndash; The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="7d87c-152">Pour plus d’informations, consultez [Introduction à gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="7d87c-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="7d87c-153">*Dossier de services* : Contient `Greeter` la mise en œuvre du service.</span><span class="sxs-lookup"><span data-stu-id="7d87c-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="7d87c-154">*appSettings.json* &ndash; contient des données de configuration, telles que le protocole utilisé par Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7d87c-154">*appSettings.json* &ndash; Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="7d87c-155">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="7d87c-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="7d87c-156">*Program.cs* &ndash; contient le point d’entrée du service gRPC.</span><span class="sxs-lookup"><span data-stu-id="7d87c-156">*Program.cs* &ndash; Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="7d87c-157">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="7d87c-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="7d87c-158">*Startup.cs* &ndash; contient le code qui configure le comportement de l’application.</span><span class="sxs-lookup"><span data-stu-id="7d87c-158">*Startup.cs* &ndash; Contains code that configures app behavior.</span></span> <span data-ttu-id="7d87c-159">Pour plus d’informations, consultez [Démarrage des applications](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="7d87c-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="7d87c-160">Créer le client gRPC dans une application console .NET</span><span class="sxs-lookup"><span data-stu-id="7d87c-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d87c-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d87c-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7d87c-162">Ouvrez une deuxième instance de Visual Studio et sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="7d87c-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="7d87c-163">Dans la boîte de dialogue **Créer un projet**, sélectionnez **Application console (.NET Core)**, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="7d87c-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="7d87c-164">Dans la zone de texte **Nom**, entrez **GrpcGreeterClient** et sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="7d87c-164">In the **Name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d87c-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d87c-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7d87c-166">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="7d87c-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7d87c-167">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="7d87c-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="7d87c-168">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="7d87c-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d87c-169">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="7d87c-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7d87c-170">Suivez les instructions de [Création d’une solution .NET Core complète sur macOS à l’aide de Visual Studio pour Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) pour créer une application console appelée *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="7d87c-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="7d87c-171">Ajouter les packages nécessaires</span><span class="sxs-lookup"><span data-stu-id="7d87c-171">Add required packages</span></span>

<span data-ttu-id="7d87c-172">Le projet client gRPC requiert les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="7d87c-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="7d87c-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), qui contient le client .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7d87c-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="7d87c-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), qui contient des API de messages protobuf pour C#.</span><span class="sxs-lookup"><span data-stu-id="7d87c-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="7d87c-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), qui contient la prise en charge des outils C# pour les fichiers protobuf.</span><span class="sxs-lookup"><span data-stu-id="7d87c-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="7d87c-176">Le package d’outils n’est pas nécessaire lors de l’exécution. La dépendance est donc marquée avec `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="7d87c-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d87c-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d87c-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7d87c-178">Installez les packages à l’aide de la console PMC (console du Gestionnaire de package) ou à partir de Gérer les packages NuGet.</span><span class="sxs-lookup"><span data-stu-id="7d87c-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="7d87c-179">Option de la console du Gestionnaire de package pour installer des packages</span><span class="sxs-lookup"><span data-stu-id="7d87c-179">PMC option to install packages</span></span>

* <span data-ttu-id="7d87c-180">De Visual Studio, sélectionnez **Tools** > **NuGet Package Manager** > **Package Manager Console**</span><span class="sxs-lookup"><span data-stu-id="7d87c-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="7d87c-181">Dans la fenêtre **Gestionnaire de package**, exécutez `cd GrpcGreeterClient` pour accéder au dossier contenant les fichiers *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="7d87c-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="7d87c-182">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="7d87c-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="7d87c-183">Option Gérer les packages NuGet pour installer les packages</span><span class="sxs-lookup"><span data-stu-id="7d87c-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="7d87c-184">Cliquez à droite sur le projet dans **Solution Explorer** > **Manage NuGet Packages**</span><span class="sxs-lookup"><span data-stu-id="7d87c-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="7d87c-185">Sélectionnez l’onglet **Parcourir**.</span><span class="sxs-lookup"><span data-stu-id="7d87c-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="7d87c-186">Entrez **Grpc.Net.Client** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="7d87c-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="7d87c-187">Sélectionnez le package **Grpc.Net.Client** sous l’onglet **Parcourir** et sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="7d87c-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="7d87c-188">Répétez ces étapes pour `Google.Protobuf` et `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="7d87c-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d87c-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d87c-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7d87c-190">Exécutez les commandes suivantes à partir du **Terminal intégré** :</span><span class="sxs-lookup"><span data-stu-id="7d87c-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d87c-191">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="7d87c-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7d87c-192">Cliquez à droite sur le dossier **Paquets** dans les**paquets Solution** **Pad** > Add</span><span class="sxs-lookup"><span data-stu-id="7d87c-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="7d87c-193">Entrez **Grpc.Net.Client** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="7d87c-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="7d87c-194">Sélectionnez le package **Grpc.Net.Client** dans le volet de résultats, puis sélectionnez **Ajouter un package**</span><span class="sxs-lookup"><span data-stu-id="7d87c-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="7d87c-195">Répétez ces étapes pour `Google.Protobuf` et `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="7d87c-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="7d87c-196">Ajouter greet.proto</span><span class="sxs-lookup"><span data-stu-id="7d87c-196">Add greet.proto</span></span>

* <span data-ttu-id="7d87c-197">Créez un dossier *Protos* dans le projet du client gRPC.</span><span class="sxs-lookup"><span data-stu-id="7d87c-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="7d87c-198">Copiez le fichier *Protos\greet.proto* du service Greeter gRPC vers le projet du client gRPC.</span><span class="sxs-lookup"><span data-stu-id="7d87c-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="7d87c-199">Modifiez le fichier projet *GrpcGreeterClient.csproj* :</span><span class="sxs-lookup"><span data-stu-id="7d87c-199">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="7d87c-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d87c-200">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="7d87c-201">Cliquez avec le bouton droit sur le projet et sélectionnez **Modifier le fichier de projet**.</span><span class="sxs-lookup"><span data-stu-id="7d87c-201">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="7d87c-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d87c-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="7d87c-203">Sélectionnez le fichier *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="7d87c-203">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d87c-204">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="7d87c-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="7d87c-205">Cliquez à droite sur le projet et sélectionnez **Tools** > **Edit File**.</span><span class="sxs-lookup"><span data-stu-id="7d87c-205">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="7d87c-206">Ajoutez un groupe d’éléments avec un élément `<Protobuf>` qui fait référence au fichier *greet.proto* :</span><span class="sxs-lookup"><span data-stu-id="7d87c-206">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="7d87c-207">Créer le client Greeter</span><span class="sxs-lookup"><span data-stu-id="7d87c-207">Create the Greeter client</span></span>

<span data-ttu-id="7d87c-208">Générez le projet pour créer les types dans l’espace de noms `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="7d87c-208">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="7d87c-209">Les types `GrpcGreeter` sont générés automatiquement par le processus de génération.</span><span class="sxs-lookup"><span data-stu-id="7d87c-209">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="7d87c-210">Mettez à jour le fichier *Program.cs* du client gRPC par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7d87c-210">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="7d87c-211">*Program.cs* contient le point d’entrée et la logique du client gRPC.</span><span class="sxs-lookup"><span data-stu-id="7d87c-211">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="7d87c-212">Le client Greeter est créé en :</span><span class="sxs-lookup"><span data-stu-id="7d87c-212">The Greeter client is created by:</span></span>

* <span data-ttu-id="7d87c-213">Instanciant un `GrpcChannel` contenant les informations de création de la connexion au service gRPC.</span><span class="sxs-lookup"><span data-stu-id="7d87c-213">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="7d87c-214">Utilisant le `GrpcChannel` pour construire le client Greeter :</span><span class="sxs-lookup"><span data-stu-id="7d87c-214">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="7d87c-215">Le client Greeter appelle la méthode `SayHello` asynchrone.</span><span class="sxs-lookup"><span data-stu-id="7d87c-215">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="7d87c-216">Le résultat de l’appel `SayHello` s’affiche :</span><span class="sxs-lookup"><span data-stu-id="7d87c-216">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="7d87c-217">Tester le client gRPC avec le service Greeter gRPC</span><span class="sxs-lookup"><span data-stu-id="7d87c-217">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d87c-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d87c-218">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7d87c-219">Dans le service Greeter, appuyez sur `Ctrl+F5` pour démarrer le serveur sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="7d87c-219">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="7d87c-220">Dans le projet `GrpcGreeterClient`, appuyez sur `Ctrl+F5` pour démarrer le client sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="7d87c-220">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d87c-221">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d87c-221">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7d87c-222">Démarrez le service Greeter.</span><span class="sxs-lookup"><span data-stu-id="7d87c-222">Start the Greeter service.</span></span>
* <span data-ttu-id="7d87c-223">Démarrez le client.</span><span class="sxs-lookup"><span data-stu-id="7d87c-223">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d87c-224">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="7d87c-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7d87c-225">Démarrez le service Greeter.</span><span class="sxs-lookup"><span data-stu-id="7d87c-225">Start the Greeter service.</span></span>
* <span data-ttu-id="7d87c-226">Démarrez le client.</span><span class="sxs-lookup"><span data-stu-id="7d87c-226">Start the client.</span></span>

---

<span data-ttu-id="7d87c-227">Le client envoie un message de salutation au service avec un message contenant son nom, *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="7d87c-227">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="7d87c-228">Le service envoie le message « Hello GreeterClient » comme réponse.</span><span class="sxs-lookup"><span data-stu-id="7d87c-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="7d87c-229">La réponse « Hello GreeterClient » s’affiche dans l’invite de commandes :</span><span class="sxs-lookup"><span data-stu-id="7d87c-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="7d87c-230">Le service gRPC enregistre les détails de l’appel réussi dans les journaux écrits dans l’invite de commandes :</span><span class="sxs-lookup"><span data-stu-id="7d87c-230">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> <span data-ttu-id="7d87c-231">Le code de cet article requiert le certificat de développement ASP.NET Core HTTPS pour sécuriser le service gRPC.</span><span class="sxs-lookup"><span data-stu-id="7d87c-231">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="7d87c-232">Si le client .NET gRPC `The remote certificate is invalid according to the validation procedure.` `The SSL connection could not be established.`échoue avec le message ou, le certificat de développement n’est pas fiable.</span><span class="sxs-lookup"><span data-stu-id="7d87c-232">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="7d87c-233">Pour résoudre ce problème, consultez [un service gRPC avec un certificat non fiable/invalide](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="7d87c-233">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="7d87c-234">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="7d87c-234">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
