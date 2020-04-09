---
title: Héberger ASP.NET Core dans un service Windows
author: rick-anderson
description: Découvrez comment héberger une application ASP.NET Core dans un service Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5cb61d330df7e15fbd54396207792596ae018fd3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417575"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="8094b-103">Héberger ASP.NET Core dans un service Windows</span><span class="sxs-lookup"><span data-stu-id="8094b-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8094b-104">Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS.</span><span class="sxs-lookup"><span data-stu-id="8094b-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="8094b-105">Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.</span><span class="sxs-lookup"><span data-stu-id="8094b-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="8094b-106">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8094b-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8094b-107">Prérequis</span><span class="sxs-lookup"><span data-stu-id="8094b-107">Prerequisites</span></span>

* [<span data-ttu-id="8094b-108">Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus</span><span class="sxs-lookup"><span data-stu-id="8094b-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="8094b-109">PowerShell version 6.2 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="8094b-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="8094b-110">Modèle Service Worker</span><span class="sxs-lookup"><span data-stu-id="8094b-110">Worker Service template</span></span>

<span data-ttu-id="8094b-111">Le modèle Service Worker ASP.NET Core fournit un point de départ pour l’écriture d’applications de service durables.</span><span class="sxs-lookup"><span data-stu-id="8094b-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="8094b-112">Pour utiliser le modèle en tant que base d’une application de service Windows :</span><span class="sxs-lookup"><span data-stu-id="8094b-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="8094b-113">Créez une application Service Worker à partir du modèle .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8094b-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="8094b-114">Suivez l’aide fournie dans la section [Configuration d’application](#app-configuration) pour mettre à jour l’application Service Worker afin qu’elle puisse s’exécuter en tant que service Windows.</span><span class="sxs-lookup"><span data-stu-id="8094b-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="8094b-115">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="8094b-115">App configuration</span></span>

<span data-ttu-id="8094b-116">L’application nécessite une référence de paquet pour [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="8094b-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="8094b-117">`IHostBuilder.UseWindowsService`est appelé lors de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="8094b-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="8094b-118">Si l’application s’exécute comme un service Windows, la méthode :</span><span class="sxs-lookup"><span data-stu-id="8094b-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="8094b-119">définit la durée de vie de l’hôte sur `WindowsServiceLifetime` ;</span><span class="sxs-lookup"><span data-stu-id="8094b-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="8094b-120">Définit la racine du [contenu](xref:fundamentals/index#content-root) à [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="8094b-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="8094b-121">Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="8094b-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="8094b-122">Permet de vous connecter au journal de l’événement :</span><span class="sxs-lookup"><span data-stu-id="8094b-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="8094b-123">Le nom de l’application est utilisé comme nom source par défaut.</span><span class="sxs-lookup"><span data-stu-id="8094b-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="8094b-124">Le niveau de journal par défaut est *Avertissement* ou plus pour `CreateDefaultBuilder` une application basée sur un modèle ASP.NET Core qui appelle à construire l’hôte.</span><span class="sxs-lookup"><span data-stu-id="8094b-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="8094b-125">Remplacer le niveau de `Logging:EventLog:LogLevel:Default` journal par défaut avec la clé dans *appsettings.json*/*appsettings. Environnement-json* ou tout autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="8094b-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="8094b-126">Seuls les administrateurs peuvent créer des sources d’événement.</span><span class="sxs-lookup"><span data-stu-id="8094b-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="8094b-127">Si une source d’événement ne peut pas être créée en utilisant le nom de l’application, un avertissement est consigné dans la source *Application* source et les journaux d’événements sont désactivés.</span><span class="sxs-lookup"><span data-stu-id="8094b-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="8094b-128">Dans `CreateHostBuilder` de *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8094b-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="8094b-129">Les exemples d’applications suivants accompagnent ce sujet :</span><span class="sxs-lookup"><span data-stu-id="8094b-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="8094b-130">Exemple &ndash; de service des travailleurs de fond Un échantillon d’application non web basé sur le [modèle de service aux travailleurs](#worker-service-template) qui utilise des services [hébergés](xref:fundamentals/host/hosted-services) pour des tâches d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="8094b-130">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="8094b-131">Web App &ndash; Service Sample A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span><span class="sxs-lookup"><span data-stu-id="8094b-131">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="8094b-132">Pour les conseils MVC, <xref:mvc/overview> <xref:migration/22-to-30>voir les articles sous et .</span><span class="sxs-lookup"><span data-stu-id="8094b-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="8094b-133">Type de déploiement</span><span class="sxs-lookup"><span data-stu-id="8094b-133">Deployment type</span></span>

<span data-ttu-id="8094b-134">Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="8094b-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="8094b-135">Kit SDK</span><span class="sxs-lookup"><span data-stu-id="8094b-135">SDK</span></span>

<span data-ttu-id="8094b-136">Pour un service web basé sur une application qui utilise les pages Razor ou les cadres MVC, spécifiez le SDK Web dans le fichier du projet :</span><span class="sxs-lookup"><span data-stu-id="8094b-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="8094b-137">Si le service n’exécute que des tâches d’arrière-plan (par exemple, [les services hébergés),](xref:fundamentals/host/hosted-services)spécifiez le Travailleur SDK dans le fichier du projet :</span><span class="sxs-lookup"><span data-stu-id="8094b-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="8094b-138">Déploiement dépendant du framework</span><span class="sxs-lookup"><span data-stu-id="8094b-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="8094b-139">Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible.</span><span class="sxs-lookup"><span data-stu-id="8094b-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="8094b-140">Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.</span><span class="sxs-lookup"><span data-stu-id="8094b-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="8094b-141">Si l’utilisation du [Web SDK](#sdk), un fichier *web.config,* qui est normalement produit lors de la publication d’une application ASP.NET Core, n’est pas nécessaire pour une application Windows Services.</span><span class="sxs-lookup"><span data-stu-id="8094b-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="8094b-142">Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="8094b-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="8094b-143">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="8094b-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="8094b-144">Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="8094b-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="8094b-145">Le runtime et les dépendances de l’application sont déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="8094b-146">Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="8094b-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="8094b-147">Pour publier pour plusieurs RID :</span><span class="sxs-lookup"><span data-stu-id="8094b-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="8094b-148">Fournissez les RID dans une liste séparée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="8094b-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="8094b-149">Utilisez le [ \<nom de propriété RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).</span><span class="sxs-lookup"><span data-stu-id="8094b-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="8094b-150">Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="8094b-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="8094b-151">Compte d’utilisateur du service</span><span class="sxs-lookup"><span data-stu-id="8094b-151">Service user account</span></span>

<span data-ttu-id="8094b-152">Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="8094b-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="8094b-153">Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :</span><span class="sxs-lookup"><span data-stu-id="8094b-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="8094b-154">Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :</span><span class="sxs-lookup"><span data-stu-id="8094b-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="8094b-155">Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.</span><span class="sxs-lookup"><span data-stu-id="8094b-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="8094b-156">Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.</span><span class="sxs-lookup"><span data-stu-id="8094b-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="8094b-157">Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="8094b-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="8094b-158">Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés.</span><span class="sxs-lookup"><span data-stu-id="8094b-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="8094b-159">Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="8094b-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="8094b-160">Droits d’ouverture de session en tant que service</span><span class="sxs-lookup"><span data-stu-id="8094b-160">Log on as a service rights</span></span>

<span data-ttu-id="8094b-161">Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="8094b-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="8094b-162">Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.</span><span class="sxs-lookup"><span data-stu-id="8094b-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="8094b-163">Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.</span><span class="sxs-lookup"><span data-stu-id="8094b-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="8094b-164">Ouvrez la stratégie **Ouvrir une session en tant que service**.</span><span class="sxs-lookup"><span data-stu-id="8094b-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="8094b-165">Sélectionnez **Ajouter un utilisateur ou un groupe**.</span><span class="sxs-lookup"><span data-stu-id="8094b-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="8094b-166">Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="8094b-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="8094b-167">Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="8094b-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="8094b-168">Sélectionnez **Avancé**.</span><span class="sxs-lookup"><span data-stu-id="8094b-168">Select **Advanced**.</span></span> <span data-ttu-id="8094b-169">Sélectionnez **Rechercher maintenant**.</span><span class="sxs-lookup"><span data-stu-id="8094b-169">Select **Find Now**.</span></span> <span data-ttu-id="8094b-170">Sélectionnez le compte d’utilisateur dans la liste.</span><span class="sxs-lookup"><span data-stu-id="8094b-170">Select the user account from the list.</span></span> <span data-ttu-id="8094b-171">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="8094b-171">Select **OK**.</span></span> <span data-ttu-id="8094b-172">Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="8094b-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="8094b-173">Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.</span><span class="sxs-lookup"><span data-stu-id="8094b-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="8094b-174">Créer et gérer le service Windows</span><span class="sxs-lookup"><span data-stu-id="8094b-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="8094b-175">Créer un service</span><span class="sxs-lookup"><span data-stu-id="8094b-175">Create a service</span></span>

<span data-ttu-id="8094b-176">Utilisez les commandes PowerShell pour enregistrer un service.</span><span class="sxs-lookup"><span data-stu-id="8094b-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="8094b-177">À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="8094b-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="8094b-178">`{EXE PATH}`&ndash; Chemin vers le dossier de l’application sur `d:\myservice`l’hôte (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-178">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="8094b-179">N’incluez pas le fichier exécutable de l’application dans le chemin.</span><span class="sxs-lookup"><span data-stu-id="8094b-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="8094b-180">Aucune barre oblique de fin n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="8094b-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="8094b-181">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Compte utilisateur de service `Contoso\ServiceUser`(par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-181">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="8094b-182">`{SERVICE NAME}`&ndash; Nom de service `MyService`(par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-182">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="8094b-183">`{EXE FILE PATH}`&ndash; Le chemin exécutable de `d:\myservice\myservice.exe`l’application (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-183">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="8094b-184">Incluez le nom de fichier de l’exécutable avec l’extension.</span><span class="sxs-lookup"><span data-stu-id="8094b-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="8094b-185">`{DESCRIPTION}`&ndash; Description de service `My sample service`(par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-185">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="8094b-186">`{DISPLAY NAME}`&ndash; Nom d’affichage de `My Service`service (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-186">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="8094b-187">Démarrer un service</span><span class="sxs-lookup"><span data-stu-id="8094b-187">Start a service</span></span>

<span data-ttu-id="8094b-188">Démarrez un service avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="8094b-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="8094b-189">La commande prend quelques secondes pour démarrer le service.</span><span class="sxs-lookup"><span data-stu-id="8094b-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="8094b-190">Déterminer l’état d’un service</span><span class="sxs-lookup"><span data-stu-id="8094b-190">Determine a service's status</span></span>

<span data-ttu-id="8094b-191">Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="8094b-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="8094b-192">L’état est signalé comme étant l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="8094b-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="8094b-193">Arrêter un service</span><span class="sxs-lookup"><span data-stu-id="8094b-193">Stop a service</span></span>

<span data-ttu-id="8094b-194">Arrêtez un service avec la commande Powershell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="8094b-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="8094b-195">Supprimer un service</span><span class="sxs-lookup"><span data-stu-id="8094b-195">Remove a service</span></span>

<span data-ttu-id="8094b-196">Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="8094b-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="8094b-197">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="8094b-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="8094b-198">Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="8094b-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="8094b-199">Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="8094b-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="8094b-200">Configuration des points de terminaison</span><span class="sxs-lookup"><span data-stu-id="8094b-200">Configure endpoints</span></span>

<span data-ttu-id="8094b-201">Par défaut, ASP.NET Core est lié à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8094b-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="8094b-202">Configurer l’URL et `ASPNETCORE_URLS` le port en définissant la variable de l’environnement.</span><span class="sxs-lookup"><span data-stu-id="8094b-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="8094b-203">Pour des approches supplémentaires de configuration d’URL et de port, consultez l’article serveur pertinent :</span><span class="sxs-lookup"><span data-stu-id="8094b-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="8094b-204">Les directives précédentes couvrent le support pour les points de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8094b-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="8094b-205">Par exemple, configurez l’application pour HTTPS lorsque l’authentification est utilisée avec un service Windows.</span><span class="sxs-lookup"><span data-stu-id="8094b-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="8094b-206">L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="8094b-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="8094b-207">Répertoire actif et racine du contenu</span><span class="sxs-lookup"><span data-stu-id="8094b-207">Current directory and content root</span></span>

<span data-ttu-id="8094b-208">Le répertoire de travail <xref:System.IO.Directory.GetCurrentDirectory*> actuel retourné en appelant à un service Windows est le dossier *C:\\WINDOWS\\system32.*</span><span class="sxs-lookup"><span data-stu-id="8094b-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="8094b-209">Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres).</span><span class="sxs-lookup"><span data-stu-id="8094b-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="8094b-210">Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.</span><span class="sxs-lookup"><span data-stu-id="8094b-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="8094b-211">Utiliser ContentRootPath ou ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="8094b-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="8094b-212">Utilisez les éléments [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> pour localiser les ressources d’une application.</span><span class="sxs-lookup"><span data-stu-id="8094b-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="8094b-213">Lorsque l’application s’exécute en tant que service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> définit le <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> vers [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="8094b-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="8094b-214">Les fichiers de paramètres par défaut de l’application, *appsettings.json* et *appsettings. Environnement-.json*, sont chargés à partir de la racine de contenu de l’application en appelant [CreateDefaultBuilder pendant](xref:fundamentals/host/generic-host#set-up-a-host)la construction hôte .</span><span class="sxs-lookup"><span data-stu-id="8094b-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="8094b-215">Pour d’autres fichiers de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>paramètres chargés par le <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>code développeur en , il n’est pas nécessaire d’appeler .</span><span class="sxs-lookup"><span data-stu-id="8094b-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="8094b-216">Dans l’exemple suivant, le fichier *custom_settings.json* existe dans la racine de contenu de l’application et est chargé sans définir explicitement un chemin de base :</span><span class="sxs-lookup"><span data-stu-id="8094b-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="8094b-217">N’essayez pas <xref:System.IO.Directory.GetCurrentDirectory*> d’utiliser pour obtenir un chemin de ressources parce qu’une application De service Windows renvoie le dossier *C:\\WINDOWS\\system32* comme répertoire actuel.</span><span class="sxs-lookup"><span data-stu-id="8094b-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="8094b-218">Stocker les fichiers d’un service dans un emplacement approprié sur le disque</span><span class="sxs-lookup"><span data-stu-id="8094b-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="8094b-219">Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.</span><span class="sxs-lookup"><span data-stu-id="8094b-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8094b-220">Dépanner</span><span class="sxs-lookup"><span data-stu-id="8094b-220">Troubleshoot</span></span>

<span data-ttu-id="8094b-221">Pour dépanner une application <xref:test/troubleshoot>Windows Service, voir .</span><span class="sxs-lookup"><span data-stu-id="8094b-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="8094b-222">Erreurs courantes</span><span class="sxs-lookup"><span data-stu-id="8094b-222">Common errors</span></span>

* <span data-ttu-id="8094b-223">Une version ancienne ou pré-version de PowerShell est utilisée.</span><span class="sxs-lookup"><span data-stu-id="8094b-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="8094b-224">Le service enregistré n’utilise pas la sortie **publiée** de l’application à partir de la commande [de publication dotnet.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="8094b-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="8094b-225">La sortie de la commande de [construction dotnet](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement de l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="8094b-226">Les actifs publiés se trouvent dans l’un ou l’autre des dossiers suivants selon le type de déploiement :</span><span class="sxs-lookup"><span data-stu-id="8094b-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="8094b-227">*bin/Release/'TARGET FRAMEWORK'/publié* (FDD)</span><span class="sxs-lookup"><span data-stu-id="8094b-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="8094b-228">*bin/Release/'TARGET FRAMEWORK'/'RUNTIME IDENTIFIER'/publier* (SCD)</span><span class="sxs-lookup"><span data-stu-id="8094b-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="8094b-229">Le service n’est pas dans l’état RUNNING.</span><span class="sxs-lookup"><span data-stu-id="8094b-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="8094b-230">Les voies vers les ressources que l’application utilise (par exemple, les certificats) sont incorrectes.</span><span class="sxs-lookup"><span data-stu-id="8094b-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="8094b-231">Le chemin de base d’un service Windows est *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="8094b-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="8094b-232">L’utilisateur n’a pas de connexion comme un droit *de service.*</span><span class="sxs-lookup"><span data-stu-id="8094b-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="8094b-233">Le mot de passe de l’utilisateur est `New-Service` expiré ou incorrectement passé lors de l’exécution de la commande PowerShell.</span><span class="sxs-lookup"><span data-stu-id="8094b-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="8094b-234">L’application nécessite ASP.NET authentification Core mais n’est pas configurée pour les connexions sécurisées (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8094b-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="8094b-235">Le port d’URL de demande est incorrect ou non configuré correctement dans l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="8094b-236">Journaux d’événements système et d’application</span><span class="sxs-lookup"><span data-stu-id="8094b-236">System and Application Event Logs</span></span>

<span data-ttu-id="8094b-237">Accédez aux journaux d’événements système et d’application :</span><span class="sxs-lookup"><span data-stu-id="8094b-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="8094b-238">Ouvrez le menu Démarrer, recherchez *le Visual event et*sélectionnez l’application Event **Viewer.**</span><span class="sxs-lookup"><span data-stu-id="8094b-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="8094b-239">Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.</span><span class="sxs-lookup"><span data-stu-id="8094b-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="8094b-240">Sélectionnez **le système** pour ouvrir le journal d’événements système.</span><span class="sxs-lookup"><span data-stu-id="8094b-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="8094b-241">Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="8094b-242">Recherchez les erreurs liées à l’application défectueuse.</span><span class="sxs-lookup"><span data-stu-id="8094b-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="8094b-243">Exécuter l’application depuis une invite de commandes</span><span class="sxs-lookup"><span data-stu-id="8094b-243">Run the app at a command prompt</span></span>

<span data-ttu-id="8094b-244">De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux d’événements.</span><span class="sxs-lookup"><span data-stu-id="8094b-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="8094b-245">Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="8094b-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="8094b-246">Pour enregistrer les détails supplémentaires de l’application, abaissez le [niveau de journal](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l’environnement [développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="8094b-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="8094b-247">Caches de paquets clairs</span><span class="sxs-lookup"><span data-stu-id="8094b-247">Clear package caches</span></span>

<span data-ttu-id="8094b-248">Une application fonctionnant peut échouer immédiatement après la mise à niveau de la SDK .NET Core sur la machine de développement ou de changer les versions de paquets dans l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="8094b-249">Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures.</span><span class="sxs-lookup"><span data-stu-id="8094b-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="8094b-250">Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :</span><span class="sxs-lookup"><span data-stu-id="8094b-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="8094b-251">Supprimez les dossiers *bin* et *obj*.</span><span class="sxs-lookup"><span data-stu-id="8094b-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="8094b-252">Effacer les caches de paquet en exécutant [dotnet nuget locaux tous - clair](/dotnet/core/tools/dotnet-nuget-locals) à partir d’une coquille de commande.</span><span class="sxs-lookup"><span data-stu-id="8094b-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="8094b-253">Les caches de paquets de compensation peuvent également être accomplies avec l’outil [nuget.exe](https://www.nuget.org/downloads) et l’exécution de la commande `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="8094b-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="8094b-254">*NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="8094b-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="8094b-255">Restaurez et regénérez le projet.</span><span class="sxs-lookup"><span data-stu-id="8094b-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="8094b-256">Supprimer tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="8094b-257">Application lente ou bloquée</span><span class="sxs-lookup"><span data-stu-id="8094b-257">Slow or hanging app</span></span>

<span data-ttu-id="8094b-258">Un *déchargement* de collision est un instantané de la mémoire du système et peut aider à déterminer la cause d’un plantage d’application, défaillance de démarrage, ou application lente.</span><span class="sxs-lookup"><span data-stu-id="8094b-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="8094b-259">L’application cesse de fonctionner ou rencontre une exception</span><span class="sxs-lookup"><span data-stu-id="8094b-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="8094b-260">Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :</span><span class="sxs-lookup"><span data-stu-id="8094b-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="8094b-261">Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="8094b-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="8094b-262">Exécutez le [script EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) avec le nom exécutable de l’application :</span><span class="sxs-lookup"><span data-stu-id="8094b-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="8094b-263">Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.</span><span class="sxs-lookup"><span data-stu-id="8094b-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="8094b-264">Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1) :</span><span class="sxs-lookup"><span data-stu-id="8094b-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="8094b-265">Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement.</span><span class="sxs-lookup"><span data-stu-id="8094b-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="8094b-266">Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.</span><span class="sxs-lookup"><span data-stu-id="8094b-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="8094b-267">Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).</span><span class="sxs-lookup"><span data-stu-id="8094b-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="8094b-268">L’application se bloque, ne démarre pas ou s’exécute normalement</span><span class="sxs-lookup"><span data-stu-id="8094b-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="8094b-269">Lorsqu’une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage, ou s’exécute normalement, voir Fichiers de décharge en [mode utilisateur: Choisir le meilleur outil](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pour sélectionner un outil approprié pour produire le dépotoir.</span><span class="sxs-lookup"><span data-stu-id="8094b-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="8094b-270">Analyser le fichier dump</span><span class="sxs-lookup"><span data-stu-id="8094b-270">Analyze the dump</span></span>

<span data-ttu-id="8094b-271">Un fichier dump peut être analysé à l’aide de plusieurs approches.</span><span class="sxs-lookup"><span data-stu-id="8094b-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="8094b-272">Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).</span><span class="sxs-lookup"><span data-stu-id="8094b-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8094b-273">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8094b-273">Additional resources</span></span>

* <span data-ttu-id="8094b-274">[Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)</span><span class="sxs-lookup"><span data-stu-id="8094b-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="8094b-275">Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS.</span><span class="sxs-lookup"><span data-stu-id="8094b-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="8094b-276">Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.</span><span class="sxs-lookup"><span data-stu-id="8094b-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="8094b-277">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8094b-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8094b-278">Prérequis</span><span class="sxs-lookup"><span data-stu-id="8094b-278">Prerequisites</span></span>

* [<span data-ttu-id="8094b-279">Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus</span><span class="sxs-lookup"><span data-stu-id="8094b-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="8094b-280">PowerShell version 6.2 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="8094b-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="8094b-281">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="8094b-281">App configuration</span></span>

<span data-ttu-id="8094b-282">L’application nécessite des références de package pour [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) et [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="8094b-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="8094b-283">Pour effectuer des tests et un débogage lors de l’exécution en dehors d’un service, ajoutez un code pour déterminer si l’application s’exécute comme un service ou comme une application console.</span><span class="sxs-lookup"><span data-stu-id="8094b-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="8094b-284">Vérifiez si le débogueur est attaché ou si un switch `--console` est présent.</span><span class="sxs-lookup"><span data-stu-id="8094b-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="8094b-285">Si l’une de ces deux conditions est remplie (l’application n’est pas exécutée en tant que service), appelez <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="8094b-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="8094b-286">Si les conditions ne sont pas remplies (l’application est exécutée en tant que service) :</span><span class="sxs-lookup"><span data-stu-id="8094b-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="8094b-287">Appelez <xref:System.IO.Directory.SetCurrentDirectory*> et utilisez un chemin vers l’emplacement publié de l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="8094b-288">N’appelez pas <xref:System.IO.Directory.GetCurrentDirectory*> pour obtenir le chemin d’accès car une application Windows Service retourne le dossier *C:\\WINDOWS\\system32* lorsque <xref:System.IO.Directory.GetCurrentDirectory*> est appelée.</span><span class="sxs-lookup"><span data-stu-id="8094b-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="8094b-289">Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="8094b-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="8094b-290">Cette étape est effectuée avant la configuration de l’application dans `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8094b-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="8094b-291">Appelez <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pour exécuter l’application en tant que service.</span><span class="sxs-lookup"><span data-stu-id="8094b-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="8094b-292">Étant donné que le [fournisseur de configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider) nécessite des paires nom/valeur pour les arguments de ligne de commande, le switch `--console` est supprimé des arguments avant que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ne les reçoive.</span><span class="sxs-lookup"><span data-stu-id="8094b-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="8094b-293">Pour consigner des informations dans le journal des événements Windows, ajoutez le fournisseur EventLog à <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="8094b-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="8094b-294">Définissez le niveau de journalisation à l’aide de clé `Logging:LogLevel:Default` dans le fichier *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="8094b-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="8094b-295">Dans l’exemple suivant, qui provient de l’exemple d’application, l’élément `RunAsCustomService` est appelé à la place de l’élément <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> afin de gérer les événements de durée de vie au sein de l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="8094b-296">Pour plus d’informations, consultez la section [Gérer les événements de démarrage et d’arrêt](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="8094b-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="8094b-297">Type de déploiement</span><span class="sxs-lookup"><span data-stu-id="8094b-297">Deployment type</span></span>

<span data-ttu-id="8094b-298">Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="8094b-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="8094b-299">Kit SDK</span><span class="sxs-lookup"><span data-stu-id="8094b-299">SDK</span></span>

<span data-ttu-id="8094b-300">Pour un service web basé sur une application qui utilise les pages Razor ou les cadres MVC, spécifiez le SDK Web dans le fichier du projet :</span><span class="sxs-lookup"><span data-stu-id="8094b-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="8094b-301">Si le service n’exécute que des tâches d’arrière-plan (par exemple, [les services hébergés),](xref:fundamentals/host/hosted-services)spécifiez le Travailleur SDK dans le fichier du projet :</span><span class="sxs-lookup"><span data-stu-id="8094b-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="8094b-302">Déploiement dépendant du framework</span><span class="sxs-lookup"><span data-stu-id="8094b-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="8094b-303">Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible.</span><span class="sxs-lookup"><span data-stu-id="8094b-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="8094b-304">Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.</span><span class="sxs-lookup"><span data-stu-id="8094b-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="8094b-305">L’identifiant Windows [Runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contient le cadre cible.</span><span class="sxs-lookup"><span data-stu-id="8094b-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="8094b-306">Dans l’exemple suivant, le RID est défini sur `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="8094b-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="8094b-307">La propriété `<SelfContained>` a la valeur `false`.</span><span class="sxs-lookup"><span data-stu-id="8094b-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="8094b-308">Ces propriétés demandent au Kit de développement logiciel (SDK) de générer un fichier exécutable (*.exe*) pour Windows et une application qui dépend du framework .NET Core partagé.</span><span class="sxs-lookup"><span data-stu-id="8094b-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="8094b-309">Un fichier *web.config*, qui est normalement produit lors de la publication d’une application ASP.NET Core, n’est pas nécessaire pour une application de Windows Services.</span><span class="sxs-lookup"><span data-stu-id="8094b-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="8094b-310">Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="8094b-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="8094b-311">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="8094b-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="8094b-312">Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="8094b-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="8094b-313">Le runtime et les dépendances de l’application sont déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="8094b-314">Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="8094b-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="8094b-315">Pour publier pour plusieurs RID :</span><span class="sxs-lookup"><span data-stu-id="8094b-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="8094b-316">Fournissez les RID dans une liste séparée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="8094b-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="8094b-317">Utilisez le [ \<nom de propriété RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).</span><span class="sxs-lookup"><span data-stu-id="8094b-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="8094b-318">Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="8094b-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="8094b-319">Une propriété `<SelfContained>` est définie sur `true` :</span><span class="sxs-lookup"><span data-stu-id="8094b-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="8094b-320">Compte d’utilisateur du service</span><span class="sxs-lookup"><span data-stu-id="8094b-320">Service user account</span></span>

<span data-ttu-id="8094b-321">Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="8094b-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="8094b-322">Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :</span><span class="sxs-lookup"><span data-stu-id="8094b-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="8094b-323">Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :</span><span class="sxs-lookup"><span data-stu-id="8094b-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="8094b-324">Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.</span><span class="sxs-lookup"><span data-stu-id="8094b-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="8094b-325">Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.</span><span class="sxs-lookup"><span data-stu-id="8094b-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="8094b-326">Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="8094b-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="8094b-327">Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés.</span><span class="sxs-lookup"><span data-stu-id="8094b-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="8094b-328">Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="8094b-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="8094b-329">Droits d’ouverture de session en tant que service</span><span class="sxs-lookup"><span data-stu-id="8094b-329">Log on as a service rights</span></span>

<span data-ttu-id="8094b-330">Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="8094b-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="8094b-331">Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.</span><span class="sxs-lookup"><span data-stu-id="8094b-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="8094b-332">Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.</span><span class="sxs-lookup"><span data-stu-id="8094b-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="8094b-333">Ouvrez la stratégie **Ouvrir une session en tant que service**.</span><span class="sxs-lookup"><span data-stu-id="8094b-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="8094b-334">Sélectionnez **Ajouter un utilisateur ou un groupe**.</span><span class="sxs-lookup"><span data-stu-id="8094b-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="8094b-335">Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="8094b-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="8094b-336">Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="8094b-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="8094b-337">Sélectionnez **Avancé**.</span><span class="sxs-lookup"><span data-stu-id="8094b-337">Select **Advanced**.</span></span> <span data-ttu-id="8094b-338">Sélectionnez **Rechercher maintenant**.</span><span class="sxs-lookup"><span data-stu-id="8094b-338">Select **Find Now**.</span></span> <span data-ttu-id="8094b-339">Sélectionnez le compte d’utilisateur dans la liste.</span><span class="sxs-lookup"><span data-stu-id="8094b-339">Select the user account from the list.</span></span> <span data-ttu-id="8094b-340">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="8094b-340">Select **OK**.</span></span> <span data-ttu-id="8094b-341">Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="8094b-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="8094b-342">Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.</span><span class="sxs-lookup"><span data-stu-id="8094b-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="8094b-343">Créer et gérer le service Windows</span><span class="sxs-lookup"><span data-stu-id="8094b-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="8094b-344">Créer un service</span><span class="sxs-lookup"><span data-stu-id="8094b-344">Create a service</span></span>

<span data-ttu-id="8094b-345">Utilisez les commandes PowerShell pour enregistrer un service.</span><span class="sxs-lookup"><span data-stu-id="8094b-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="8094b-346">À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="8094b-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="8094b-347">`{EXE PATH}`&ndash; Chemin vers le dossier de l’application sur `d:\myservice`l’hôte (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-347">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="8094b-348">N’incluez pas le fichier exécutable de l’application dans le chemin.</span><span class="sxs-lookup"><span data-stu-id="8094b-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="8094b-349">Aucune barre oblique de fin n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="8094b-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="8094b-350">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Compte utilisateur de service `Contoso\ServiceUser`(par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-350">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="8094b-351">`{SERVICE NAME}`&ndash; Nom de service `MyService`(par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-351">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="8094b-352">`{EXE FILE PATH}`&ndash; Le chemin exécutable de `d:\myservice\myservice.exe`l’application (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-352">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="8094b-353">Incluez le nom de fichier de l’exécutable avec l’extension.</span><span class="sxs-lookup"><span data-stu-id="8094b-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="8094b-354">`{DESCRIPTION}`&ndash; Description de service `My sample service`(par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-354">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="8094b-355">`{DISPLAY NAME}`&ndash; Nom d’affichage de `My Service`service (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-355">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="8094b-356">Démarrer un service</span><span class="sxs-lookup"><span data-stu-id="8094b-356">Start a service</span></span>

<span data-ttu-id="8094b-357">Démarrez un service avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="8094b-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="8094b-358">La commande prend quelques secondes pour démarrer le service.</span><span class="sxs-lookup"><span data-stu-id="8094b-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="8094b-359">Déterminer l’état d’un service</span><span class="sxs-lookup"><span data-stu-id="8094b-359">Determine a service's status</span></span>

<span data-ttu-id="8094b-360">Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="8094b-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="8094b-361">L’état est signalé comme étant l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="8094b-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="8094b-362">Arrêter un service</span><span class="sxs-lookup"><span data-stu-id="8094b-362">Stop a service</span></span>

<span data-ttu-id="8094b-363">Arrêtez un service avec la commande Powershell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="8094b-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="8094b-364">Supprimer un service</span><span class="sxs-lookup"><span data-stu-id="8094b-364">Remove a service</span></span>

<span data-ttu-id="8094b-365">Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="8094b-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="8094b-366">Gérer les événements de démarrage et d’arrêt</span><span class="sxs-lookup"><span data-stu-id="8094b-366">Handle starting and stopping events</span></span>

<span data-ttu-id="8094b-367">Pour gérer les événements <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> et <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="8094b-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="8094b-368">Créez une classe qui dérive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> à l’aide des méthodes `OnStarting`, `OnStarted` et `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="8094b-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="8094b-369">Créez une méthode d’extension pour <xref:Microsoft.AspNetCore.Hosting.IWebHost> qui transmet `CustomWebHostService` à <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="8094b-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="8094b-370">Dans `Program.Main`, appelez la méthode d’extension `RunAsCustomService` au lieu de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="8094b-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="8094b-371">Pour afficher l’emplacement de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> dans `Program.Main`, reportez-vous à l’exemple de code indiqué dans la section [Type de déploiement](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="8094b-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="8094b-372">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="8094b-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="8094b-373">Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="8094b-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="8094b-374">Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="8094b-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="8094b-375">Configuration des points de terminaison</span><span class="sxs-lookup"><span data-stu-id="8094b-375">Configure endpoints</span></span>

<span data-ttu-id="8094b-376">Par défaut, ASP.NET Core est lié à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8094b-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="8094b-377">Configurer l’URL et `ASPNETCORE_URLS` le port en définissant la variable de l’environnement.</span><span class="sxs-lookup"><span data-stu-id="8094b-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="8094b-378">Pour des approches supplémentaires de configuration d’URL et de port, consultez l’article serveur pertinent :</span><span class="sxs-lookup"><span data-stu-id="8094b-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="8094b-379">Les directives précédentes couvrent le support pour les points de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8094b-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="8094b-380">Par exemple, configurez l’application pour HTTPS lorsque l’authentification est utilisée avec un service Windows.</span><span class="sxs-lookup"><span data-stu-id="8094b-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="8094b-381">L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="8094b-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="8094b-382">Répertoire actif et racine du contenu</span><span class="sxs-lookup"><span data-stu-id="8094b-382">Current directory and content root</span></span>

<span data-ttu-id="8094b-383">Le répertoire de travail <xref:System.IO.Directory.GetCurrentDirectory*> actuel retourné en appelant à un service Windows est le dossier *C:\\WINDOWS\\system32.*</span><span class="sxs-lookup"><span data-stu-id="8094b-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="8094b-384">Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres).</span><span class="sxs-lookup"><span data-stu-id="8094b-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="8094b-385">Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.</span><span class="sxs-lookup"><span data-stu-id="8094b-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="8094b-386">Définir le dossier de l’application comme chemin d’accès racine du contenu</span><span class="sxs-lookup"><span data-stu-id="8094b-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="8094b-387">La chaîne <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> correspond au même chemin que celui fourni à l’argument `binPath` lorsqu’un service est créé.</span><span class="sxs-lookup"><span data-stu-id="8094b-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="8094b-388">Au lieu `GetCurrentDirectory` d’appeler pour créer <xref:System.IO.Directory.SetCurrentDirectory*> des chemins vers des fichiers de paramètres, appelez avec le chemin vers la racine de [contenu](xref:fundamentals/index#content-root)de l’application .</span><span class="sxs-lookup"><span data-stu-id="8094b-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="8094b-389">Dans `Program.Main`, définissez le chemin d’accès au dossier du fichier exécutable du service ainsi que le chemin d’accès pour établir la racine du contenu de l’application :</span><span class="sxs-lookup"><span data-stu-id="8094b-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="8094b-390">Stocker les fichiers d’un service dans un emplacement approprié sur le disque</span><span class="sxs-lookup"><span data-stu-id="8094b-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="8094b-391">Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.</span><span class="sxs-lookup"><span data-stu-id="8094b-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8094b-392">Dépanner</span><span class="sxs-lookup"><span data-stu-id="8094b-392">Troubleshoot</span></span>

<span data-ttu-id="8094b-393">Pour dépanner une application <xref:test/troubleshoot>Windows Service, voir .</span><span class="sxs-lookup"><span data-stu-id="8094b-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="8094b-394">Erreurs courantes</span><span class="sxs-lookup"><span data-stu-id="8094b-394">Common errors</span></span>

* <span data-ttu-id="8094b-395">Une version ancienne ou pré-version de PowerShell est utilisée.</span><span class="sxs-lookup"><span data-stu-id="8094b-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="8094b-396">Le service enregistré n’utilise pas la sortie **publiée** de l’application à partir de la commande [de publication dotnet.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="8094b-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="8094b-397">La sortie de la commande de [construction dotnet](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement de l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="8094b-398">Les actifs publiés se trouvent dans l’un ou l’autre des dossiers suivants selon le type de déploiement :</span><span class="sxs-lookup"><span data-stu-id="8094b-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="8094b-399">*bin/Release/'TARGET FRAMEWORK'/publié* (FDD)</span><span class="sxs-lookup"><span data-stu-id="8094b-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="8094b-400">*bin/Release/'TARGET FRAMEWORK'/'RUNTIME IDENTIFIER'/publier* (SCD)</span><span class="sxs-lookup"><span data-stu-id="8094b-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="8094b-401">Le service n’est pas dans l’état RUNNING.</span><span class="sxs-lookup"><span data-stu-id="8094b-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="8094b-402">Les voies vers les ressources que l’application utilise (par exemple, les certificats) sont incorrectes.</span><span class="sxs-lookup"><span data-stu-id="8094b-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="8094b-403">Le chemin de base d’un service Windows est *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="8094b-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="8094b-404">L’utilisateur n’a pas de connexion comme un droit *de service.*</span><span class="sxs-lookup"><span data-stu-id="8094b-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="8094b-405">Le mot de passe de l’utilisateur est `New-Service` expiré ou incorrectement passé lors de l’exécution de la commande PowerShell.</span><span class="sxs-lookup"><span data-stu-id="8094b-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="8094b-406">L’application nécessite ASP.NET authentification Core mais n’est pas configurée pour les connexions sécurisées (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8094b-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="8094b-407">Le port d’URL de demande est incorrect ou non configuré correctement dans l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="8094b-408">Journaux d’événements système et d’application</span><span class="sxs-lookup"><span data-stu-id="8094b-408">System and Application Event Logs</span></span>

<span data-ttu-id="8094b-409">Accédez aux journaux d’événements système et d’application :</span><span class="sxs-lookup"><span data-stu-id="8094b-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="8094b-410">Ouvrez le menu Démarrer, recherchez *le Visual event et*sélectionnez l’application Event **Viewer.**</span><span class="sxs-lookup"><span data-stu-id="8094b-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="8094b-411">Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.</span><span class="sxs-lookup"><span data-stu-id="8094b-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="8094b-412">Sélectionnez **le système** pour ouvrir le journal d’événements système.</span><span class="sxs-lookup"><span data-stu-id="8094b-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="8094b-413">Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="8094b-414">Recherchez les erreurs liées à l’application défectueuse.</span><span class="sxs-lookup"><span data-stu-id="8094b-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="8094b-415">Exécuter l’application depuis une invite de commandes</span><span class="sxs-lookup"><span data-stu-id="8094b-415">Run the app at a command prompt</span></span>

<span data-ttu-id="8094b-416">De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux d’événements.</span><span class="sxs-lookup"><span data-stu-id="8094b-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="8094b-417">Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="8094b-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="8094b-418">Pour enregistrer les détails supplémentaires de l’application, abaissez le [niveau de journal](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l’environnement [développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="8094b-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="8094b-419">Caches de paquets clairs</span><span class="sxs-lookup"><span data-stu-id="8094b-419">Clear package caches</span></span>

<span data-ttu-id="8094b-420">Une application fonctionnant peut échouer immédiatement après la mise à niveau de la SDK .NET Core sur la machine de développement ou de changer les versions de paquets dans l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="8094b-421">Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures.</span><span class="sxs-lookup"><span data-stu-id="8094b-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="8094b-422">Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :</span><span class="sxs-lookup"><span data-stu-id="8094b-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="8094b-423">Supprimez les dossiers *bin* et *obj*.</span><span class="sxs-lookup"><span data-stu-id="8094b-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="8094b-424">Effacer les caches de paquet en exécutant [dotnet nuget locaux tous - clair](/dotnet/core/tools/dotnet-nuget-locals) à partir d’une coquille de commande.</span><span class="sxs-lookup"><span data-stu-id="8094b-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="8094b-425">Les caches de paquets de compensation peuvent également être accomplies avec l’outil [nuget.exe](https://www.nuget.org/downloads) et l’exécution de la commande `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="8094b-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="8094b-426">*NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="8094b-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="8094b-427">Restaurez et regénérez le projet.</span><span class="sxs-lookup"><span data-stu-id="8094b-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="8094b-428">Supprimer tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="8094b-429">Application lente ou bloquée</span><span class="sxs-lookup"><span data-stu-id="8094b-429">Slow or hanging app</span></span>

<span data-ttu-id="8094b-430">Un *déchargement* de collision est un instantané de la mémoire du système et peut aider à déterminer la cause d’un plantage d’application, défaillance de démarrage, ou application lente.</span><span class="sxs-lookup"><span data-stu-id="8094b-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="8094b-431">L’application cesse de fonctionner ou rencontre une exception</span><span class="sxs-lookup"><span data-stu-id="8094b-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="8094b-432">Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :</span><span class="sxs-lookup"><span data-stu-id="8094b-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="8094b-433">Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="8094b-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="8094b-434">Exécutez le [script EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) avec le nom exécutable de l’application :</span><span class="sxs-lookup"><span data-stu-id="8094b-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="8094b-435">Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.</span><span class="sxs-lookup"><span data-stu-id="8094b-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="8094b-436">Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) :</span><span class="sxs-lookup"><span data-stu-id="8094b-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="8094b-437">Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement.</span><span class="sxs-lookup"><span data-stu-id="8094b-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="8094b-438">Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.</span><span class="sxs-lookup"><span data-stu-id="8094b-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="8094b-439">Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).</span><span class="sxs-lookup"><span data-stu-id="8094b-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="8094b-440">L’application se bloque, ne démarre pas ou s’exécute normalement</span><span class="sxs-lookup"><span data-stu-id="8094b-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="8094b-441">Lorsqu’une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage, ou s’exécute normalement, voir Fichiers de décharge en [mode utilisateur: Choisir le meilleur outil](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pour sélectionner un outil approprié pour produire le dépotoir.</span><span class="sxs-lookup"><span data-stu-id="8094b-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="8094b-442">Analyser le fichier dump</span><span class="sxs-lookup"><span data-stu-id="8094b-442">Analyze the dump</span></span>

<span data-ttu-id="8094b-443">Un fichier dump peut être analysé à l’aide de plusieurs approches.</span><span class="sxs-lookup"><span data-stu-id="8094b-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="8094b-444">Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).</span><span class="sxs-lookup"><span data-stu-id="8094b-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8094b-445">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8094b-445">Additional resources</span></span>

* <span data-ttu-id="8094b-446">[Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)</span><span class="sxs-lookup"><span data-stu-id="8094b-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="8094b-447">Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS.</span><span class="sxs-lookup"><span data-stu-id="8094b-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="8094b-448">Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.</span><span class="sxs-lookup"><span data-stu-id="8094b-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="8094b-449">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8094b-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8094b-450">Prérequis</span><span class="sxs-lookup"><span data-stu-id="8094b-450">Prerequisites</span></span>

* [<span data-ttu-id="8094b-451">Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus</span><span class="sxs-lookup"><span data-stu-id="8094b-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="8094b-452">PowerShell version 6.2 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="8094b-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="8094b-453">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="8094b-453">App configuration</span></span>

<span data-ttu-id="8094b-454">L’application nécessite des références de package pour [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) et [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="8094b-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="8094b-455">Pour effectuer des tests et un débogage lors de l’exécution en dehors d’un service, ajoutez un code pour déterminer si l’application s’exécute comme un service ou comme une application console.</span><span class="sxs-lookup"><span data-stu-id="8094b-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="8094b-456">Vérifiez si le débogueur est attaché ou si un switch `--console` est présent.</span><span class="sxs-lookup"><span data-stu-id="8094b-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="8094b-457">Si l’une de ces deux conditions est remplie (l’application n’est pas exécutée en tant que service), appelez <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="8094b-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="8094b-458">Si les conditions ne sont pas remplies (l’application est exécutée en tant que service) :</span><span class="sxs-lookup"><span data-stu-id="8094b-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="8094b-459">Appelez <xref:System.IO.Directory.SetCurrentDirectory*> et utilisez un chemin vers l’emplacement publié de l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="8094b-460">N’appelez pas <xref:System.IO.Directory.GetCurrentDirectory*> pour obtenir le chemin d’accès car une application Windows Service retourne le dossier *C:\\WINDOWS\\system32* lorsque <xref:System.IO.Directory.GetCurrentDirectory*> est appelée.</span><span class="sxs-lookup"><span data-stu-id="8094b-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="8094b-461">Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="8094b-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="8094b-462">Cette étape est effectuée avant la configuration de l’application dans `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8094b-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="8094b-463">Appelez <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pour exécuter l’application en tant que service.</span><span class="sxs-lookup"><span data-stu-id="8094b-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="8094b-464">Étant donné que le [fournisseur de configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider) nécessite des paires nom/valeur pour les arguments de ligne de commande, le switch `--console` est supprimé des arguments avant que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ne les reçoive.</span><span class="sxs-lookup"><span data-stu-id="8094b-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="8094b-465">Pour consigner des informations dans le journal des événements Windows, ajoutez le fournisseur EventLog à <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="8094b-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="8094b-466">Définissez le niveau de journalisation à l’aide de clé `Logging:LogLevel:Default` dans le fichier *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="8094b-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="8094b-467">Dans l’exemple suivant, qui provient de l’exemple d’application, l’élément `RunAsCustomService` est appelé à la place de l’élément <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> afin de gérer les événements de durée de vie au sein de l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="8094b-468">Pour plus d’informations, consultez la section [Gérer les événements de démarrage et d’arrêt](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="8094b-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="8094b-469">Type de déploiement</span><span class="sxs-lookup"><span data-stu-id="8094b-469">Deployment type</span></span>

<span data-ttu-id="8094b-470">Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="8094b-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="8094b-471">Kit SDK</span><span class="sxs-lookup"><span data-stu-id="8094b-471">SDK</span></span>

<span data-ttu-id="8094b-472">Pour un service web basé sur une application qui utilise les pages Razor ou les cadres MVC, spécifiez le SDK Web dans le fichier du projet :</span><span class="sxs-lookup"><span data-stu-id="8094b-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="8094b-473">Si le service n’exécute que des tâches d’arrière-plan (par exemple, [les services hébergés),](xref:fundamentals/host/hosted-services)spécifiez le Travailleur SDK dans le fichier du projet :</span><span class="sxs-lookup"><span data-stu-id="8094b-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="8094b-474">Déploiement dépendant du framework</span><span class="sxs-lookup"><span data-stu-id="8094b-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="8094b-475">Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible.</span><span class="sxs-lookup"><span data-stu-id="8094b-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="8094b-476">Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.</span><span class="sxs-lookup"><span data-stu-id="8094b-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="8094b-477">L’identifiant Windows [Runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contient le cadre cible.</span><span class="sxs-lookup"><span data-stu-id="8094b-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="8094b-478">Dans l’exemple suivant, le RID est défini sur `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="8094b-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="8094b-479">La propriété `<SelfContained>` a la valeur `false`.</span><span class="sxs-lookup"><span data-stu-id="8094b-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="8094b-480">Ces propriétés demandent au Kit de développement logiciel (SDK) de générer un fichier exécutable (*.exe*) pour Windows et une application qui dépend du framework .NET Core partagé.</span><span class="sxs-lookup"><span data-stu-id="8094b-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="8094b-481">La propriété `<UseAppHost>` a la valeur `true`.</span><span class="sxs-lookup"><span data-stu-id="8094b-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="8094b-482">Cette propriété fournit au service un chemin d’activation (un fichier exécutable *.exe*) pour un déploiement dépendant du framework (FDD).</span><span class="sxs-lookup"><span data-stu-id="8094b-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="8094b-483">Un fichier *web.config*, qui est normalement produit lors de la publication d’une application ASP.NET Core, n’est pas nécessaire pour une application de Windows Services.</span><span class="sxs-lookup"><span data-stu-id="8094b-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="8094b-484">Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="8094b-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="8094b-485">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="8094b-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="8094b-486">Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="8094b-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="8094b-487">Le runtime et les dépendances de l’application sont déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="8094b-488">Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="8094b-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="8094b-489">Pour publier pour plusieurs RID :</span><span class="sxs-lookup"><span data-stu-id="8094b-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="8094b-490">Fournissez les RID dans une liste séparée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="8094b-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="8094b-491">Utilisez le [ \<nom de propriété RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).</span><span class="sxs-lookup"><span data-stu-id="8094b-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="8094b-492">Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="8094b-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="8094b-493">Une propriété `<SelfContained>` est définie sur `true` :</span><span class="sxs-lookup"><span data-stu-id="8094b-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="8094b-494">Compte d’utilisateur du service</span><span class="sxs-lookup"><span data-stu-id="8094b-494">Service user account</span></span>

<span data-ttu-id="8094b-495">Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="8094b-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="8094b-496">Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :</span><span class="sxs-lookup"><span data-stu-id="8094b-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="8094b-497">Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :</span><span class="sxs-lookup"><span data-stu-id="8094b-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="8094b-498">Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.</span><span class="sxs-lookup"><span data-stu-id="8094b-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="8094b-499">Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.</span><span class="sxs-lookup"><span data-stu-id="8094b-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="8094b-500">Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="8094b-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="8094b-501">Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés.</span><span class="sxs-lookup"><span data-stu-id="8094b-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="8094b-502">Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="8094b-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="8094b-503">Droits d’ouverture de session en tant que service</span><span class="sxs-lookup"><span data-stu-id="8094b-503">Log on as a service rights</span></span>

<span data-ttu-id="8094b-504">Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="8094b-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="8094b-505">Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.</span><span class="sxs-lookup"><span data-stu-id="8094b-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="8094b-506">Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.</span><span class="sxs-lookup"><span data-stu-id="8094b-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="8094b-507">Ouvrez la stratégie **Ouvrir une session en tant que service**.</span><span class="sxs-lookup"><span data-stu-id="8094b-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="8094b-508">Sélectionnez **Ajouter un utilisateur ou un groupe**.</span><span class="sxs-lookup"><span data-stu-id="8094b-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="8094b-509">Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="8094b-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="8094b-510">Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="8094b-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="8094b-511">Sélectionnez **Avancé**.</span><span class="sxs-lookup"><span data-stu-id="8094b-511">Select **Advanced**.</span></span> <span data-ttu-id="8094b-512">Sélectionnez **Rechercher maintenant**.</span><span class="sxs-lookup"><span data-stu-id="8094b-512">Select **Find Now**.</span></span> <span data-ttu-id="8094b-513">Sélectionnez le compte d’utilisateur dans la liste.</span><span class="sxs-lookup"><span data-stu-id="8094b-513">Select the user account from the list.</span></span> <span data-ttu-id="8094b-514">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="8094b-514">Select **OK**.</span></span> <span data-ttu-id="8094b-515">Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="8094b-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="8094b-516">Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.</span><span class="sxs-lookup"><span data-stu-id="8094b-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="8094b-517">Créer et gérer le service Windows</span><span class="sxs-lookup"><span data-stu-id="8094b-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="8094b-518">Créer un service</span><span class="sxs-lookup"><span data-stu-id="8094b-518">Create a service</span></span>

<span data-ttu-id="8094b-519">Utilisez les commandes PowerShell pour enregistrer un service.</span><span class="sxs-lookup"><span data-stu-id="8094b-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="8094b-520">À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="8094b-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="8094b-521">`{EXE PATH}`&ndash; Chemin vers le dossier de l’application sur `d:\myservice`l’hôte (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-521">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="8094b-522">N’incluez pas le fichier exécutable de l’application dans le chemin.</span><span class="sxs-lookup"><span data-stu-id="8094b-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="8094b-523">Aucune barre oblique de fin n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="8094b-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="8094b-524">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Compte utilisateur de service `Contoso\ServiceUser`(par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-524">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="8094b-525">`{SERVICE NAME}`&ndash; Nom de service `MyService`(par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-525">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="8094b-526">`{EXE FILE PATH}`&ndash; Le chemin exécutable de `d:\myservice\myservice.exe`l’application (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-526">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="8094b-527">Incluez le nom de fichier de l’exécutable avec l’extension.</span><span class="sxs-lookup"><span data-stu-id="8094b-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="8094b-528">`{DESCRIPTION}`&ndash; Description de service `My sample service`(par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-528">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="8094b-529">`{DISPLAY NAME}`&ndash; Nom d’affichage de `My Service`service (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="8094b-529">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="8094b-530">Démarrer un service</span><span class="sxs-lookup"><span data-stu-id="8094b-530">Start a service</span></span>

<span data-ttu-id="8094b-531">Démarrez un service avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="8094b-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="8094b-532">La commande prend quelques secondes pour démarrer le service.</span><span class="sxs-lookup"><span data-stu-id="8094b-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="8094b-533">Déterminer l’état d’un service</span><span class="sxs-lookup"><span data-stu-id="8094b-533">Determine a service's status</span></span>

<span data-ttu-id="8094b-534">Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="8094b-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="8094b-535">L’état est signalé comme étant l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="8094b-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="8094b-536">Arrêter un service</span><span class="sxs-lookup"><span data-stu-id="8094b-536">Stop a service</span></span>

<span data-ttu-id="8094b-537">Arrêtez un service avec la commande Powershell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="8094b-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="8094b-538">Supprimer un service</span><span class="sxs-lookup"><span data-stu-id="8094b-538">Remove a service</span></span>

<span data-ttu-id="8094b-539">Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :</span><span class="sxs-lookup"><span data-stu-id="8094b-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="8094b-540">Gérer les événements de démarrage et d’arrêt</span><span class="sxs-lookup"><span data-stu-id="8094b-540">Handle starting and stopping events</span></span>

<span data-ttu-id="8094b-541">Pour gérer les événements <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> et <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="8094b-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="8094b-542">Créez une classe qui dérive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> à l’aide des méthodes `OnStarting`, `OnStarted` et `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="8094b-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="8094b-543">Créez une méthode d’extension pour <xref:Microsoft.AspNetCore.Hosting.IWebHost> qui transmet `CustomWebHostService` à <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="8094b-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="8094b-544">Dans `Program.Main`, appelez la méthode d’extension `RunAsCustomService` au lieu de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="8094b-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="8094b-545">Pour afficher l’emplacement de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> dans `Program.Main`, reportez-vous à l’exemple de code indiqué dans la section [Type de déploiement](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="8094b-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="8094b-546">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="8094b-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="8094b-547">Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="8094b-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="8094b-548">Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="8094b-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="8094b-549">Configuration des points de terminaison</span><span class="sxs-lookup"><span data-stu-id="8094b-549">Configure endpoints</span></span>

<span data-ttu-id="8094b-550">Par défaut, ASP.NET Core est lié à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8094b-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="8094b-551">Configurer l’URL et `ASPNETCORE_URLS` le port en définissant la variable de l’environnement.</span><span class="sxs-lookup"><span data-stu-id="8094b-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="8094b-552">Pour des approches supplémentaires de configuration d’URL et de port, consultez l’article serveur pertinent :</span><span class="sxs-lookup"><span data-stu-id="8094b-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="8094b-553">Les directives précédentes couvrent le support pour les points de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8094b-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="8094b-554">Par exemple, configurez l’application pour HTTPS lorsque l’authentification est utilisée avec un service Windows.</span><span class="sxs-lookup"><span data-stu-id="8094b-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="8094b-555">L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="8094b-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="8094b-556">Répertoire actif et racine du contenu</span><span class="sxs-lookup"><span data-stu-id="8094b-556">Current directory and content root</span></span>

<span data-ttu-id="8094b-557">Le répertoire de travail <xref:System.IO.Directory.GetCurrentDirectory*> actuel retourné en appelant à un service Windows est le dossier *C:\\WINDOWS\\system32.*</span><span class="sxs-lookup"><span data-stu-id="8094b-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="8094b-558">Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres).</span><span class="sxs-lookup"><span data-stu-id="8094b-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="8094b-559">Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.</span><span class="sxs-lookup"><span data-stu-id="8094b-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="8094b-560">Définir le dossier de l’application comme chemin d’accès racine du contenu</span><span class="sxs-lookup"><span data-stu-id="8094b-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="8094b-561">La chaîne <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> correspond au même chemin que celui fourni à l’argument `binPath` lorsqu’un service est créé.</span><span class="sxs-lookup"><span data-stu-id="8094b-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="8094b-562">Au lieu `GetCurrentDirectory` d’appeler pour créer <xref:System.IO.Directory.SetCurrentDirectory*> des chemins vers des fichiers de paramètres, appelez avec le chemin vers la racine de [contenu](xref:fundamentals/index#content-root)de l’application .</span><span class="sxs-lookup"><span data-stu-id="8094b-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="8094b-563">Dans `Program.Main`, définissez le chemin d’accès au dossier du fichier exécutable du service ainsi que le chemin d’accès pour établir la racine du contenu de l’application :</span><span class="sxs-lookup"><span data-stu-id="8094b-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="8094b-564">Stocker les fichiers d’un service dans un emplacement approprié sur le disque</span><span class="sxs-lookup"><span data-stu-id="8094b-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="8094b-565">Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.</span><span class="sxs-lookup"><span data-stu-id="8094b-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8094b-566">Dépanner</span><span class="sxs-lookup"><span data-stu-id="8094b-566">Troubleshoot</span></span>

<span data-ttu-id="8094b-567">Pour dépanner une application <xref:test/troubleshoot>Windows Service, voir .</span><span class="sxs-lookup"><span data-stu-id="8094b-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="8094b-568">Erreurs courantes</span><span class="sxs-lookup"><span data-stu-id="8094b-568">Common errors</span></span>

* <span data-ttu-id="8094b-569">Une version ancienne ou pré-version de PowerShell est utilisée.</span><span class="sxs-lookup"><span data-stu-id="8094b-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="8094b-570">Le service enregistré n’utilise pas la sortie **publiée** de l’application à partir de la commande [de publication dotnet.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="8094b-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="8094b-571">La sortie de la commande de [construction dotnet](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement de l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="8094b-572">Les actifs publiés se trouvent dans l’un ou l’autre des dossiers suivants selon le type de déploiement :</span><span class="sxs-lookup"><span data-stu-id="8094b-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="8094b-573">*bin/Release/'TARGET FRAMEWORK'/publié* (FDD)</span><span class="sxs-lookup"><span data-stu-id="8094b-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="8094b-574">*bin/Release/'TARGET FRAMEWORK'/'RUNTIME IDENTIFIER'/publier* (SCD)</span><span class="sxs-lookup"><span data-stu-id="8094b-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="8094b-575">Le service n’est pas dans l’état RUNNING.</span><span class="sxs-lookup"><span data-stu-id="8094b-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="8094b-576">Les voies vers les ressources que l’application utilise (par exemple, les certificats) sont incorrectes.</span><span class="sxs-lookup"><span data-stu-id="8094b-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="8094b-577">Le chemin de base d’un service Windows est *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="8094b-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="8094b-578">L’utilisateur n’a pas de connexion comme un droit *de service.*</span><span class="sxs-lookup"><span data-stu-id="8094b-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="8094b-579">Le mot de passe de l’utilisateur est `New-Service` expiré ou incorrectement passé lors de l’exécution de la commande PowerShell.</span><span class="sxs-lookup"><span data-stu-id="8094b-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="8094b-580">L’application nécessite ASP.NET authentification Core mais n’est pas configurée pour les connexions sécurisées (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8094b-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="8094b-581">Le port d’URL de demande est incorrect ou non configuré correctement dans l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="8094b-582">Journaux d’événements système et d’application</span><span class="sxs-lookup"><span data-stu-id="8094b-582">System and Application Event Logs</span></span>

<span data-ttu-id="8094b-583">Accédez aux journaux d’événements système et d’application :</span><span class="sxs-lookup"><span data-stu-id="8094b-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="8094b-584">Ouvrez le menu Démarrer, recherchez *le Visual event et*sélectionnez l’application Event **Viewer.**</span><span class="sxs-lookup"><span data-stu-id="8094b-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="8094b-585">Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.</span><span class="sxs-lookup"><span data-stu-id="8094b-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="8094b-586">Sélectionnez **le système** pour ouvrir le journal d’événements système.</span><span class="sxs-lookup"><span data-stu-id="8094b-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="8094b-587">Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="8094b-588">Recherchez les erreurs liées à l’application défectueuse.</span><span class="sxs-lookup"><span data-stu-id="8094b-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="8094b-589">Exécuter l’application depuis une invite de commandes</span><span class="sxs-lookup"><span data-stu-id="8094b-589">Run the app at a command prompt</span></span>

<span data-ttu-id="8094b-590">De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux d’événements.</span><span class="sxs-lookup"><span data-stu-id="8094b-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="8094b-591">Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte.</span><span class="sxs-lookup"><span data-stu-id="8094b-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="8094b-592">Pour enregistrer les détails supplémentaires de l’application, abaissez le [niveau de journal](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l’environnement [développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="8094b-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="8094b-593">Caches de paquets clairs</span><span class="sxs-lookup"><span data-stu-id="8094b-593">Clear package caches</span></span>

<span data-ttu-id="8094b-594">Une application fonctionnant peut échouer immédiatement après la mise à niveau de la SDK .NET Core sur la machine de développement ou de changer les versions de paquets dans l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="8094b-595">Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures.</span><span class="sxs-lookup"><span data-stu-id="8094b-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="8094b-596">Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :</span><span class="sxs-lookup"><span data-stu-id="8094b-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="8094b-597">Supprimez les dossiers *bin* et *obj*.</span><span class="sxs-lookup"><span data-stu-id="8094b-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="8094b-598">Effacer les caches de paquet en exécutant [dotnet nuget locaux tous - clair](/dotnet/core/tools/dotnet-nuget-locals) à partir d’une coquille de commande.</span><span class="sxs-lookup"><span data-stu-id="8094b-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="8094b-599">Les caches de paquets de compensation peuvent également être accomplies avec l’outil [nuget.exe](https://www.nuget.org/downloads) et l’exécution de la commande `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="8094b-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="8094b-600">*NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="8094b-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="8094b-601">Restaurez et regénérez le projet.</span><span class="sxs-lookup"><span data-stu-id="8094b-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="8094b-602">Supprimer tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="8094b-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="8094b-603">Application lente ou bloquée</span><span class="sxs-lookup"><span data-stu-id="8094b-603">Slow or hanging app</span></span>

<span data-ttu-id="8094b-604">Un *déchargement* de collision est un instantané de la mémoire du système et peut aider à déterminer la cause d’un plantage d’application, défaillance de démarrage, ou application lente.</span><span class="sxs-lookup"><span data-stu-id="8094b-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="8094b-605">L’application cesse de fonctionner ou rencontre une exception</span><span class="sxs-lookup"><span data-stu-id="8094b-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="8094b-606">Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :</span><span class="sxs-lookup"><span data-stu-id="8094b-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="8094b-607">Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="8094b-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="8094b-608">Exécutez le [script EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) avec le nom exécutable de l’application :</span><span class="sxs-lookup"><span data-stu-id="8094b-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="8094b-609">Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.</span><span class="sxs-lookup"><span data-stu-id="8094b-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="8094b-610">Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) :</span><span class="sxs-lookup"><span data-stu-id="8094b-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="8094b-611">Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement.</span><span class="sxs-lookup"><span data-stu-id="8094b-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="8094b-612">Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.</span><span class="sxs-lookup"><span data-stu-id="8094b-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="8094b-613">Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).</span><span class="sxs-lookup"><span data-stu-id="8094b-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="8094b-614">L’application se bloque, ne démarre pas ou s’exécute normalement</span><span class="sxs-lookup"><span data-stu-id="8094b-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="8094b-615">Lorsqu’une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage, ou s’exécute normalement, voir Fichiers de décharge en [mode utilisateur: Choisir le meilleur outil](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pour sélectionner un outil approprié pour produire le dépotoir.</span><span class="sxs-lookup"><span data-stu-id="8094b-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="8094b-616">Analyser le fichier dump</span><span class="sxs-lookup"><span data-stu-id="8094b-616">Analyze the dump</span></span>

<span data-ttu-id="8094b-617">Un fichier dump peut être analysé à l’aide de plusieurs approches.</span><span class="sxs-lookup"><span data-stu-id="8094b-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="8094b-618">Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).</span><span class="sxs-lookup"><span data-stu-id="8094b-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8094b-619">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8094b-619">Additional resources</span></span>

* <span data-ttu-id="8094b-620">[Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)</span><span class="sxs-lookup"><span data-stu-id="8094b-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
