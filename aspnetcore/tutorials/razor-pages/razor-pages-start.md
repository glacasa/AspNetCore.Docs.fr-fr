---
title: 'Didacticiel : prise en main des Razor pages dans ASP.net Core'
author: rick-anderson
description: Cette série de didacticiels montre comment utiliser Razor des pages dans ASP.net core. Découvrez comment créer un modèle, générer du code pour les Razor pages, utiliser des Entity Framework Core et des SQL Server pour l’accès aux données, ajouter des fonctionnalités de recherche, ajouter une validation d’entrée et utiliser des migrations pour mettre à jour le modèle.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 97e3f60480bc8e7e88c8361e5b13f02d98765d9e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405300"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Didacticiel : prise en main des Razor pages dans ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Il s’agit du premier didacticiel d’une série qui enseigne les bases de la création d’une Razor application web ASP.net Core pages.

[!INCLUDE[](~/includes/advancedRP.md)]

À la fin de la série, vous disposez d’une application qui gère une base de données de films.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer une Razor application Web pages.
> * Exécutez l'application.
> * Examiner les fichiers projet.

À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail sur laquelle vous allez vous appuyer dans des didacticiels ultérieurs.

![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Créer une Razor application Web pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.
* Créez une application web ASP.NET Core, puis sélectionnez **Suivant**.
  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Nommez le projet **RazorPagesMovie**. Il est important de nommer le projet *RazorPagesMovie* pour que les espaces de noms correspondent quand vous copiez et collez du code.
  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

* Sélectionnez **ASP.NET Core 3,1** dans la liste déroulante, **application Web**, puis sélectionnez **créer**.

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  Le projet de démarrage suivant est créé :

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Accédez au répertoire (`cd`) qui contiendra le projet.

* Exécutez les commandes suivantes :

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * La `dotnet new` commande crée un Razor projet de pages dans le dossier *RazorPagesMovie* .
  * La commande `code` ouvre le dossier *RazorPagesMovie* dans l’instance actuelle de Visual Studio Code.

* Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « RazorPagesMovie ». Ajoutez-les ?** Sélectionnez **Oui**.

  Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

  ![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **Web Application**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application Web application**console  >  **suivant**.

  ![sélection du modèle d’application Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* Vérifiez les configurations suivantes :

  * **Framework cible** défini sur **.net Core 3,1**.
  * **L’authentification** est définie sur **aucune authentification**.
   
  Sélectionnez **Suivant**.

  ![sélection de macOS .NET Core 3,1](razor-pages-start/_static/targetframework3.png)

* Nommez le projet **RazorPagesMovie**, puis sélectionnez **Créer**.

  ![macOS nom du projet](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Exécuter l’application

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Examiner les fichiers projet

Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.

### <a name="pages-folder"></a>Dossier Pages

Contient Razor des pages et des fichiers de prise en charge. Chaque Razor page est une paire de fichiers :

* Un fichier *. cshtml* qui contient le balisage HTML avec du code C# à l’aide de la Razor syntaxe.
* Un fichier *. cshtml.cs* qui contient du code C# gérant les événements de page.

Les fichiers de prise en charge ont des noms commençant par un trait de soulignement. Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages. Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page. Pour plus d’informations, consultez <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Dossier racine

Contient des fichiers statiques, tels que les fichiers HTML, JavaScript et CSS. Pour plus d’informations, consultez <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contient les données de configuration, comme les chaînes de connexion. Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contient le point d’entrée pour le programme. Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

contient le code qui configure le comportement de l’application. Pour plus d’informations, consultez <xref:fundamentals/startup>.

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant dans la série :

> [!div class="step-by-step"]
> [Ajouter un modèle](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Ceci est le premier didacticiel d’une série. [La série](xref:tutorials/razor-pages/index) enseigne les bases de la création d’une Razor application Web ASP.net Core pages.

[!INCLUDE[](~/includes/advancedRP.md)]

À la fin de la série, vous disposez d’une application qui gère une base de données de films.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer une Razor application Web pages.
> * Exécutez l'application.
> * Examiner les fichiers projet.

À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail sur laquelle vous allez vous appuyer dans des didacticiels ultérieurs.

![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Créer une Razor application Web pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.

* Créez une application web ASP.NET Core, puis sélectionnez **Suivant**.

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Nommez le projet **RazorPagesMovie**. Il est important de nommer le projet *RazorPagesMovie* pour que les espaces de noms correspondent quand vous copiez et collez du code.

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

* Sélectionnez **ASP.NET Core 2.2** dans la liste déroulante, sélectionnez **Application web**, puis **Créer**.

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  Le projet de démarrage suivant est créé :

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Accédez au répertoire (`cd`) qui contiendra le projet.

* Exécutez les commandes suivantes :

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * La `dotnet new` commande crée un Razor projet de pages dans le dossier *RazorPagesMovie* .
  * La commande `code` ouvre le dossier *RazorPagesMovie* dans l’instance actuelle de Visual Studio Code.

* Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « RazorPagesMovie ». Ajoutez-les ?** Sélectionnez **Oui**.

  Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **Web Application**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application Web application**console  >  **suivant**.

* Dans la boîte de dialogue **configurer votre nouvelle API Web ASP.net Core** , définissez **Framework cible** sur **.net Core 3,1**.

  ![Sélection de .NET Core 3.0 pour macOS](razor-pages-start/_static/targetframework3.png)

* Nommez le projet **RazorPagesMovie**, puis sélectionnez **Créer**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Exécuter l’application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application. La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.

* Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.

  Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.

  ![Page d’accueil ou d’index](razor-pages-start/_static/homeGDPR2.2.png)

  L’illustration suivante montre l’application après avoir consenti au suivi :

  ![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Appuyez sur **Ctrl+F5** pour exécuter sans le débogueur.

  Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`. La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local.

* Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.

  Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.

  ![Page d’accueil ou d’index](razor-pages-start/_static/homeGDPR2.2.png)

  L’illustration suivante montre l’application après avoir consenti au suivi :

  ![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Appuyez sur **Cmd+Opt+F5** pour lancer l’exécution sans le débogueur.

  Visual Studio démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`.

* Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.

  Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.

  ![Page d’accueil ou d’index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  L’illustration suivante montre l’application après avoir consenti au suivi :

  ![Page d’accueil ou d’index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Examiner les fichiers projet

Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.

### <a name="pages-folder"></a>Dossier Pages

Contient Razor des pages et des fichiers de prise en charge. Chaque Razor page est une paire de fichiers :

* Un fichier *. cshtml* qui contient le balisage HTML avec du code C# à l’aide de la Razor syntaxe.
* Un fichier *. cshtml.cs* qui contient du code C# gérant les événements de page.

Les fichiers de prise en charge ont des noms commençant par un trait de soulignement. Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages. Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page. Pour plus d’informations, consultez <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Dossier racine

Contient des fichiers statiques, tels que les fichiers HTML, JavaScript et CSS. Pour plus d’informations, consultez <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contient les données de configuration, comme les chaînes de connexion. Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contient le point d’entrée pour le programme. Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Contient le code qui configure le comportement de l’application, comme le fait qu’elle exige un consentement pour les cookies. Pour plus d’informations, consultez <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce didacticiel](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant dans la série :

> [!div class="step-by-step"]
> [Ajouter un modèle](xref:tutorials/razor-pages/model)

::: moniker-end
