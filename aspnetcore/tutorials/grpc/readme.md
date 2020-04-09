---
page_type: sample
description: Ce tutoriel montre comment créer un service gRPC et un client gRPC sur ASP.NET Core.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: b9feb9eed62177358fffc0d7da582f625a431e32
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660929"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a><span data-ttu-id="cb6f9-102">Créer un serveur et un client gRPC dans ASP.NET Core 3.0 avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb6f9-102">Create a gRPC client and server in ASP.NET Core 3.0 using Visual Studio</span></span>

<span data-ttu-id="cb6f9-103">Ce tutoriel montre comment créer un client [gRPC](https://grpc.io/docs/guides/) .NET Core et un serveur gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-103">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="cb6f9-104">À la fin, vous disposerez d’un client gRPC qui communique avec le service Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-104">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="cb6f9-105">Dans ce tutoriel, vous allez effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="cb6f9-105">In this tutorial, you;</span></span>

* <span data-ttu-id="cb6f9-106">Créer un serveur gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-106">Create a gRPC Server.</span></span>
* <span data-ttu-id="cb6f9-107">Créez un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-107">Create a gRPC client.</span></span>
* <span data-ttu-id="cb6f9-108">Tester le service du client gRPC avec le service Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-108">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="create-a-grpc-service"></a><span data-ttu-id="cb6f9-109">Créer un service gRPC</span><span class="sxs-lookup"><span data-stu-id="cb6f9-109">Create a gRPC service</span></span>

* <span data-ttu-id="cb6f9-110">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-110">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="cb6f9-111">Dans la boîte de dialogue **Créer un projet**, sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-111">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="cb6f9-112">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-112">Select **Next**</span></span>
* <span data-ttu-id="cb6f9-113">Nommez le projet **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-113">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="cb6f9-114">Il est important de nommer le projet *GrpcGreeter* pour que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-114">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="cb6f9-115">Sélectionnez **Créer**</span><span class="sxs-lookup"><span data-stu-id="cb6f9-115">Select **Create**</span></span>
* <span data-ttu-id="cb6f9-116">Dans la boîte de dialogue **Créer une application web ASP.NET Core** :</span><span class="sxs-lookup"><span data-stu-id="cb6f9-116">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="cb6f9-117">Sélectionnez **.NET Core** et **ASP.NET Core 3.0** dans les menus déroulants.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-117">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="cb6f9-118">Sélectionnez le modèle **Service gRPC**.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-118">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="cb6f9-119">Sélectionnez **Créer**</span><span class="sxs-lookup"><span data-stu-id="cb6f9-119">Select **Create**</span></span>

### <a name="run-the-service"></a><span data-ttu-id="cb6f9-120">Exécuter le service</span><span class="sxs-lookup"><span data-stu-id="cb6f9-120">Run the service</span></span>

* <span data-ttu-id="cb6f9-121">Appuyez sur `Ctrl+F5` pour exécuter le service gRPC sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-121">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="cb6f9-122">Visual Studio exécute le service dans une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-122">Visual Studio runs the service in a command prompt.</span></span>

<span data-ttu-id="cb6f9-123">Les journaux indiquent que le service écoute sur `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-123">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

> [!NOTE]
> <span data-ttu-id="cb6f9-124">Le modèle gRPC est configuré pour utiliser le protocole [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="cb6f9-124">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="cb6f9-125">Les clients gRPC doivent utiliser le protocole HTTPS pour appeler le serveur.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-125">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="cb6f9-126">MacOS ne prend pas en charge ASP.NET Core gRPC avec TLS.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-126">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="cb6f9-127">Une configuration supplémentaire est nécessaire pour exécuter correctement les services gRPC sur MacOS.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-127">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="cb6f9-128">Pour plus d’informations, consultez [gRPC et ASP.NET Core sur macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span><span class="sxs-lookup"><span data-stu-id="cb6f9-128">For more information, see [gRPC and ASP.NET Core on macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="cb6f9-129">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="cb6f9-129">Examine the project files</span></span>

<span data-ttu-id="cb6f9-130">Fichiers projet *GrpcGreeter* :</span><span class="sxs-lookup"><span data-stu-id="cb6f9-130">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="cb6f9-131">*greet.proto*: Le fichier *Protos/greet.proto* définit le `Greeter` gRPC et est utilisé pour générer les actifs du serveur gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-131">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="cb6f9-132">Pour plus d’informations, consultez [Introduction à gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="cb6f9-132">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="cb6f9-133">*Dossier de services* : Contient `Greeter` la mise en œuvre du service.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-133">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="cb6f9-134">*appSettings.json*: Contient des données de configuration, telles que le protocole utilisé par Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-134">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="cb6f9-135">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-135">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="cb6f9-136">*Program.cs*: Contient le point d’entrée du service gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-136">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="cb6f9-137">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-137">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="cb6f9-138">*Startup.cs*: Contient du code qui configure le comportement de l’application.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-138">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="cb6f9-139">Pour plus d’informations, consultez [Démarrage des applications](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="cb6f9-139">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="cb6f9-140">Créer le client gRPC dans une application console .NET</span><span class="sxs-lookup"><span data-stu-id="cb6f9-140">Create the gRPC client in a .NET console app</span></span>

* <span data-ttu-id="cb6f9-141">Ouvrez une deuxième instance de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-141">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="cb6f9-142">Sélectionnez **File** > **New** > **Project** à partir de la barre de menu.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-142">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="cb6f9-143">Dans la boîte de dialogue **Créer un projet**, sélectionnez **Application console (.NET Core)**.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-143">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="cb6f9-144">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-144">Select **Next**</span></span>
* <span data-ttu-id="cb6f9-145">Dans la zone de texte **Nom**, entrez « GrpcGreeterClient ».</span><span class="sxs-lookup"><span data-stu-id="cb6f9-145">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="cb6f9-146">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="cb6f9-146">Select **Create**.</span></span>

### <a name="add-required-packages"></a><span data-ttu-id="cb6f9-147">Ajouter les packages nécessaires</span><span class="sxs-lookup"><span data-stu-id="cb6f9-147">Add required packages</span></span>

<span data-ttu-id="cb6f9-148">Le projet client gRPC requiert les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="cb6f9-148">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="cb6f9-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), qui contient le client .NET Core.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="cb6f9-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), qui contient des API de messages protobuf pour C#.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="cb6f9-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), qui contient la prise en charge des outils C# pour les fichiers protobuf.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="cb6f9-152">Le package d’outils n’est pas nécessaire lors de l’exécution. La dépendance est donc marquée avec `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-152">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="cb6f9-153">Installez les packages à l’aide de la console PMC (console du Gestionnaire de package) ou à partir de Gérer les packages NuGet.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-153">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="cb6f9-154">Option de la console du Gestionnaire de package pour installer des packages</span><span class="sxs-lookup"><span data-stu-id="cb6f9-154">PMC option to install packages</span></span>

* <span data-ttu-id="cb6f9-155">De Visual Studio, sélectionnez **Tools** > **NuGet Package Manager** > **Package Manager Console**</span><span class="sxs-lookup"><span data-stu-id="cb6f9-155">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="cb6f9-156">Dans la fenêtre **Console du Gestionnaire de Package**, accédez au répertoire où se trouve le fichier *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-156">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="cb6f9-157">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="cb6f9-157">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="cb6f9-158">Option Gérer les packages NuGet pour installer les packages</span><span class="sxs-lookup"><span data-stu-id="cb6f9-158">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="cb6f9-159">Cliquez à droite sur le projet dans **Solution Explorer** > **Manage NuGet Packages**</span><span class="sxs-lookup"><span data-stu-id="cb6f9-159">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="cb6f9-160">Sélectionnez l’onglet **Parcourir**.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-160">Select the **Browse** tab.</span></span>
* <span data-ttu-id="cb6f9-161">Entrez **Grpc.Net.Client** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-161">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="cb6f9-162">Sélectionnez le package **Grpc.Net.Client** sous l’onglet **Parcourir** et sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-162">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="cb6f9-163">Répétez ces étapes pour `Google.Protobuf` et `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-163">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="add-greetproto"></a><span data-ttu-id="cb6f9-164">Ajouter greet.proto</span><span class="sxs-lookup"><span data-stu-id="cb6f9-164">Add greet.proto</span></span>

* <span data-ttu-id="cb6f9-165">Créez un dossier **Protos** dans le projet du client gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-165">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="cb6f9-166">Copiez le fichier **Protos\greet.proto** du service Greeter gRPC vers le projet du client gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-166">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="cb6f9-167">Modifiez le fichier projet *GrpcGreeterClient.csproj* :</span><span class="sxs-lookup"><span data-stu-id="cb6f9-167">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  <span data-ttu-id="cb6f9-168">Cliquez avec le bouton droit sur le projet et sélectionnez **Modifier le fichier de projet**.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-168">Right-click the project and select **Edit Project File**.</span></span>

* <span data-ttu-id="cb6f9-169">Ajoutez un groupe d’éléments avec un élément `<Protobuf>` qui fait référence au fichier **greet.proto** :</span><span class="sxs-lookup"><span data-stu-id="cb6f9-169">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="cb6f9-170">Créer le client Greeter</span><span class="sxs-lookup"><span data-stu-id="cb6f9-170">Create the Greeter client</span></span>

<span data-ttu-id="cb6f9-171">Générez le projet pour créer les types dans l’espace de noms `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-171">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="cb6f9-172">Les types `GrpcGreeter` sont générés automatiquement par le processus de génération.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-172">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="cb6f9-173">Mettez à jour le fichier *Program.cs* du client gRPC par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cb6f9-173">Update the gRPC client *Program.cs* file with the following code:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using GrpcGreeter;
using Grpc.Net.Client;

namespace GrpcGreeterClient
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // The port number(5001) must match the port of the gRPC server.
            var channel = GrpcChannel.ForAddress("https://localhost:5001");
            var client = new Greeter.GreeterClient(channel);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="cb6f9-174">*Program.cs* contient le point d’entrée et la logique du client gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-174">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="cb6f9-175">Le client Greeter est créé en :</span><span class="sxs-lookup"><span data-stu-id="cb6f9-175">The Greeter client is created by:</span></span>

* <span data-ttu-id="cb6f9-176">Instanciant un `GrpcChannel` contenant les informations de création de la connexion au service gRPC.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-176">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="cb6f9-177">Utilisation `GrpcChannel` du client Greeter pour construire.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-177">Using the `GrpcChannel` to construct the Greeter client.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="cb6f9-178">Tester le client gRPC avec le service Greeter gRPC</span><span class="sxs-lookup"><span data-stu-id="cb6f9-178">Test the gRPC client with the gRPC Greeter service</span></span>

* <span data-ttu-id="cb6f9-179">Dans le service Greeter, appuyez sur `Ctrl+F5` pour démarrer le serveur sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-179">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="cb6f9-180">Dans le projet `GrpcGreeterClient`, appuyez sur `Ctrl+F5` pour démarrer le client sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-180">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

<span data-ttu-id="cb6f9-181">Le client envoie une salutation au service avec un message contenant son nom, « GreeterClient ».</span><span class="sxs-lookup"><span data-stu-id="cb6f9-181">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="cb6f9-182">Le service envoie le message « Hello GreeterClient » comme réponse.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-182">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="cb6f9-183">La réponse « Hello GreeterClient » s’affiche dans l’invite de commandes :</span><span class="sxs-lookup"><span data-stu-id="cb6f9-183">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="cb6f9-184">Le service gRPC enregistre les détails de l’appel réussi dans les journaux écrits dans l’invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="cb6f9-184">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="docs-help--next-steps-for-grpc"></a><span data-ttu-id="cb6f9-185">Documents d’aide et étapes suivantes pour gRPC</span><span class="sxs-lookup"><span data-stu-id="cb6f9-185">Docs help & next steps for gRPC</span></span>

* [<span data-ttu-id="cb6f9-186">Présentation de gRPC sur ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb6f9-186">Introduction to gRPC on ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [<span data-ttu-id="cb6f9-187">Services gRPC avec C#</span><span class="sxs-lookup"><span data-stu-id="cb6f9-187">gRPC services with C#</span></span>](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [<span data-ttu-id="cb6f9-188">Migration des services gRPC de C Core vers ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb6f9-188">Migrating gRPC services from C-core to ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
