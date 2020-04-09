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
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a>Créer un serveur et un client gRPC dans ASP.NET Core 3.0 avec Visual Studio

Ce tutoriel montre comment créer un client [gRPC](https://grpc.io/docs/guides/) .NET Core et un serveur gRPC ASP.NET Core.

À la fin, vous disposerez d’un client gRPC qui communique avec le service Greeter gRPC.

Dans ce tutoriel, vous allez effectuer les actions suivantes :

* Créer un serveur gRPC.
* Créez un client gRPC.
* Tester le service du client gRPC avec le service Greeter gRPC.

## <a name="create-a-grpc-service"></a>Créer un service gRPC

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.
* Dans la boîte de dialogue **Créer un projet**, sélectionnez **Application web ASP.NET Core**.
* Sélectionnez **Suivant**.
* Nommez le projet **GrpcGreeter**. Il est important de nommer le projet *GrpcGreeter* pour que les espaces de noms correspondent quand vous copiez et collez du code.
* Sélectionnez **Créer**
* Dans la boîte de dialogue **Créer une application web ASP.NET Core** :
  * Sélectionnez **.NET Core** et **ASP.NET Core 3.0** dans les menus déroulants. 
  * Sélectionnez le modèle **Service gRPC**.
  * Sélectionnez **Créer**

### <a name="run-the-service"></a>Exécuter le service

* Appuyez sur `Ctrl+F5` pour exécuter le service gRPC sans le débogueur.

  Visual Studio exécute le service dans une invite de commandes.

Les journaux indiquent que le service écoute sur `https://localhost:5001`.

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
> Le modèle gRPC est configuré pour utiliser le protocole [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246). Les clients gRPC doivent utiliser le protocole HTTPS pour appeler le serveur.
>
> MacOS ne prend pas en charge ASP.NET Core gRPC avec TLS. Une configuration supplémentaire est nécessaire pour exécuter correctement les services gRPC sur MacOS. Pour plus d’informations, consultez [gRPC et ASP.NET Core sur macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).

### <a name="examine-the-project-files"></a>Examiner les fichiers projet

Fichiers projet *GrpcGreeter* :

* *greet.proto*: Le fichier *Protos/greet.proto* définit le `Greeter` gRPC et est utilisé pour générer les actifs du serveur gRPC. Pour plus d’informations, consultez [Introduction à gRPC](xref:grpc/index).
* *Dossier de services* : Contient `Greeter` la mise en œuvre du service.
* *appSettings.json*: Contient des données de configuration, telles que le protocole utilisé par Kestrel. Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.
* *Program.cs*: Contient le point d’entrée du service gRPC. Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.
* *Startup.cs*: Contient du code qui configure le comportement de l’application. Pour plus d’informations, consultez [Démarrage des applications](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Créer le client gRPC dans une application console .NET

* Ouvrez une deuxième instance de Visual Studio.
* Sélectionnez **File** > **New** > **Project** à partir de la barre de menu.
* Dans la boîte de dialogue **Créer un projet**, sélectionnez **Application console (.NET Core)**.
* Sélectionnez **Suivant**.
* Dans la zone de texte **Nom**, entrez « GrpcGreeterClient ».
* Sélectionnez **Create** (Créer).

### <a name="add-required-packages"></a>Ajouter les packages nécessaires

Le projet client gRPC requiert les packages suivants :

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), qui contient le client .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), qui contient des API de messages protobuf pour C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), qui contient la prise en charge des outils C# pour les fichiers protobuf. Le package d’outils n’est pas nécessaire lors de l’exécution. La dépendance est donc marquée avec `PrivateAssets="All"`.

Installez les packages à l’aide de la console PMC (console du Gestionnaire de package) ou à partir de Gérer les packages NuGet.

#### <a name="pmc-option-to-install-packages"></a>Option de la console du Gestionnaire de package pour installer des packages

* De Visual Studio, sélectionnez **Tools** > **NuGet Package Manager** > **Package Manager Console**
* Dans la fenêtre **Console du Gestionnaire de Package**, accédez au répertoire où se trouve le fichier *GrpcGreeterClient.csproj*.
* Exécutez les commandes suivantes :

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Option Gérer les packages NuGet pour installer les packages

* Cliquez à droite sur le projet dans **Solution Explorer** > **Manage NuGet Packages**
* Sélectionnez l’onglet **Parcourir**.
* Entrez **Grpc.Net.Client** dans la zone de recherche.
* Sélectionnez le package **Grpc.Net.Client** sous l’onglet **Parcourir** et sélectionnez **Installer**.
* Répétez ces étapes pour `Google.Protobuf` et `Grpc.Tools`.

### <a name="add-greetproto"></a>Ajouter greet.proto

* Créez un dossier **Protos** dans le projet du client gRPC.
* Copiez le fichier **Protos\greet.proto** du service Greeter gRPC vers le projet du client gRPC.
* Modifiez le fichier projet *GrpcGreeterClient.csproj* :

  Cliquez avec le bouton droit sur le projet et sélectionnez **Modifier le fichier de projet**.

* Ajoutez un groupe d’éléments avec un élément `<Protobuf>` qui fait référence au fichier **greet.proto** :

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Créer le client Greeter

Générez le projet pour créer les types dans l’espace de noms `GrpcGreeter`. Les types `GrpcGreeter` sont générés automatiquement par le processus de génération.

Mettez à jour le fichier *Program.cs* du client gRPC par le code suivant :

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

*Program.cs* contient le point d’entrée et la logique du client gRPC.

Le client Greeter est créé en :

* Instanciant un `GrpcChannel` contenant les informations de création de la connexion au service gRPC.
* Utilisation `GrpcChannel` du client Greeter pour construire.

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Tester le client gRPC avec le service Greeter gRPC

* Dans le service Greeter, appuyez sur `Ctrl+F5` pour démarrer le serveur sans le débogueur.
* Dans le projet `GrpcGreeterClient`, appuyez sur `Ctrl+F5` pour démarrer le client sans le débogueur.

Le client envoie une salutation au service avec un message contenant son nom, « GreeterClient ». Le service envoie le message « Hello GreeterClient » comme réponse. La réponse « Hello GreeterClient » s’affiche dans l’invite de commandes :

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Le service gRPC enregistre les détails de l’appel réussi dans les journaux écrits dans l’invite de commandes.

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

### <a name="docs-help--next-steps-for-grpc"></a>Documents d’aide et étapes suivantes pour gRPC

* [Présentation de gRPC sur ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [Services gRPC avec C#](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [Migration des services gRPC de C Core vers ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
