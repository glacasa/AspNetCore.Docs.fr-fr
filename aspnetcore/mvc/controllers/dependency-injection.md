---
title: Injection de dépendances dans les contrôleurs dans ASP.NET Core
author: ardalis
description: Découvrez comment les contrôleurs ASP.NET Core MVC demandent explicitement leurs dépendances par le biais de leurs constructeurs avec l’injection de dépendances dans ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 67861b2cdb946f7cd630770507181ee853b7fbfd
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408420"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="7bc86-103">Injection de dépendances dans les contrôleurs dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7bc86-103">Dependency injection into controllers in ASP.NET Core</span></span>

<a name="dependency-injection-controllers"></a>

<span data-ttu-id="7bc86-104">Par [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="7bc86-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="7bc86-105">Les contrôleurs ASP.NET Core MVC demandent les dépendances explicitement via des constructeurs.</span><span class="sxs-lookup"><span data-stu-id="7bc86-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="7bc86-106">ASP.NET Core offre une prise en charge intégrée de l’[injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7bc86-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7bc86-107">L’injection de dépendances facilite le test et la maintenance des applications.</span><span class="sxs-lookup"><span data-stu-id="7bc86-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="7bc86-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7bc86-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="7bc86-109">Injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="7bc86-109">Constructor Injection</span></span>

<span data-ttu-id="7bc86-110">Les services sont ajoutés sous forme de paramètre de constructeur, et le runtime résout les services à partir du conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="7bc86-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="7bc86-111">Les services sont généralement définis à partir d’interfaces.</span><span class="sxs-lookup"><span data-stu-id="7bc86-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="7bc86-112">Par exemple, prenons le cas d’une application qui a besoin de l’heure actuelle.</span><span class="sxs-lookup"><span data-stu-id="7bc86-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="7bc86-113">L’interface suivante expose le service `IDateTime` :</span><span class="sxs-lookup"><span data-stu-id="7bc86-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="7bc86-114">Le code suivant implémente l’interface `IDateTime` :</span><span class="sxs-lookup"><span data-stu-id="7bc86-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="7bc86-115">Ajoutez le service au conteneur de services :</span><span class="sxs-lookup"><span data-stu-id="7bc86-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="7bc86-116">Pour plus d’informations sur <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, consultez [Durée de vie des services d’injonction de dépendances](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="7bc86-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="7bc86-117">Le code suivant adresse une salutation à l’utilisateur qui varie en fonction de l’heure du jour :</span><span class="sxs-lookup"><span data-stu-id="7bc86-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="7bc86-118">Exécutez l’application et un message s’affiche en fonction de l’heure.</span><span class="sxs-lookup"><span data-stu-id="7bc86-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="7bc86-119">Injection d’action avec FromServices</span><span class="sxs-lookup"><span data-stu-id="7bc86-119">Action injection with FromServices</span></span>

<span data-ttu-id="7bc86-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> permet d’injecter un service directement dans une méthode d’action sans utiliser l’injection de constructeurs :</span><span class="sxs-lookup"><span data-stu-id="7bc86-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="7bc86-121">Accéder aux paramètres à partir d’un contrôleur</span><span class="sxs-lookup"><span data-stu-id="7bc86-121">Access settings from a controller</span></span>

<span data-ttu-id="7bc86-122">L’accès aux paramètres de configuration ou d’application à partir d’un contrôleur est un modèle commun.</span><span class="sxs-lookup"><span data-stu-id="7bc86-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="7bc86-123">Le *modèle options* décrit dans <xref:fundamentals/configuration/options> est l’approche à privilégier pour gérer les paramètres.</span><span class="sxs-lookup"><span data-stu-id="7bc86-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="7bc86-124">En règle générale, n’injectez pas directement <xref:Microsoft.Extensions.Configuration.IConfiguration> dans un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7bc86-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="7bc86-125">Créez une classe qui représente les options.</span><span class="sxs-lookup"><span data-stu-id="7bc86-125">Create a class that represents the options.</span></span> <span data-ttu-id="7bc86-126">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="7bc86-126">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="7bc86-127">Ajoutez la classe de configuration à la collection de services :</span><span class="sxs-lookup"><span data-stu-id="7bc86-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="7bc86-128">Configurez l’application pour qu’elle lise les paramètres à partir d’un fichier au format JSON :</span><span class="sxs-lookup"><span data-stu-id="7bc86-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="7bc86-129">Le code suivant demande les paramètres `IOptions<SampleWebSettings>` au conteneur de services et les utilise dans la méthode `Index` :</span><span class="sxs-lookup"><span data-stu-id="7bc86-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="7bc86-130">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7bc86-130">Additional resources</span></span>

* <span data-ttu-id="7bc86-131">Consultez <xref:mvc/controllers/testing> pour savoir comment rendre le code plus facile à tester en demandant explicitement des dépendances dans les contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="7bc86-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="7bc86-132">[Remplacez le conteneur d’injection de dépendances par défaut par une implémentation tierce](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="7bc86-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>
