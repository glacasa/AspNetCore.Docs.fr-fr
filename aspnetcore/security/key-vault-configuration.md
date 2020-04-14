---
title: Fournisseur de configuration de coffre-fort Azure dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser le fournisseur de configurations De coffre-fort Azure pour configurer une application à l’aide de paires de valeurs nominatives chargées au moment de l’exécution.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228164"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="a0ade-103">Fournisseur de configuration de coffre-fort Azure dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0ade-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="a0ade-104">Par [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="a0ade-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a0ade-105">Ce document explique comment utiliser le fournisseur [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Pour charger les valeurs de configuration des applications à partir des secrets Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0ade-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="a0ade-106">Azure Key Vault est un service basé sur le cloud qui aide à protéger les clés cryptographiques et les secrets utilisés par les applications et les services.</span><span class="sxs-lookup"><span data-stu-id="a0ade-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="a0ade-107">Les scénarios courants pour l’utilisation d’Azure Key Vault avec des applications ASP.NET Core incluent :</span><span class="sxs-lookup"><span data-stu-id="a0ade-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="a0ade-108">Contrôle de l’accès aux données de configuration sensibles.</span><span class="sxs-lookup"><span data-stu-id="a0ade-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="a0ade-109">Répondre à l’exigence de FIPS 140-2 Niveau 2 validé modules de sécurité matérielle (HSM) lors du stockage des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0ade-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="a0ade-110">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a0ade-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="a0ade-111">.</span><span class="sxs-lookup"><span data-stu-id="a0ade-111">Packages</span></span>

<span data-ttu-id="a0ade-112">Ajoutez une référence de paquet au package [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)</span><span class="sxs-lookup"><span data-stu-id="a0ade-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="a0ade-113">Exemple d'application</span><span class="sxs-lookup"><span data-stu-id="a0ade-113">Sample app</span></span>

<span data-ttu-id="a0ade-114">L’application d’échantillon s’exécute `#define` dans l’un des deux modes déterminés par la déclaration en haut du fichier *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="a0ade-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="a0ade-115">`Certificate`&ndash; Démontre l’utilisation d’un id De client Azure Key Vault et d’un certificat X.509 pour accéder aux secrets stockés dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0ade-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="a0ade-116">Cette version de l’échantillon peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou tout hôte capable de servir une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a0ade-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="a0ade-117">`Managed`&ndash; Démontre comment utiliser [les identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l’application à Azure Key Vault avec l’authentification Azure AD sans informations d’identification stockées dans le code ou la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="a0ade-118">Lors de l’utilisation d’identités gérées pour authentifier, un ID d’application Azure AD et mot de passe (Secret client) ne sont pas nécessaires.</span><span class="sxs-lookup"><span data-stu-id="a0ade-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="a0ade-119">La `Managed` version de l’échantillon doit être déployée à Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="a0ade-120">Suivez les conseils dans la section [Utiliser les identités gérées pour les ressources Azure.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="a0ade-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="a0ade-121">Pour plus d’informations sur la façon de`#define`configurer <xref:index#preprocessor-directives-in-sample-code>une application d’échantillon à l’aide de directives de préprocesseur ( ), voir .</span><span class="sxs-lookup"><span data-stu-id="a0ade-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="a0ade-122">Stockage secret dans l’environnement de développement</span><span class="sxs-lookup"><span data-stu-id="a0ade-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="a0ade-123">Définissez des secrets localement à l’aide de [l’outil Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a0ade-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a0ade-124">Lorsque l’application d’échantillon s’exécute sur la machine locale dans l’environnement de développement, les secrets sont chargés à partir du magasin secret manager local.</span><span class="sxs-lookup"><span data-stu-id="a0ade-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="a0ade-125">L’outil Secret `<UserSecretsId>` Manager nécessite une propriété dans le fichier de projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="a0ade-126">Définissez la`{GUID}`valeur de la propriété ( ) à n’importe quel GUID unique :</span><span class="sxs-lookup"><span data-stu-id="a0ade-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="a0ade-127">Les secrets sont créés comme paires de valeurs nom.</span><span class="sxs-lookup"><span data-stu-id="a0ade-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="a0ade-128">Les valeurs hiérarchiques (sections de configuration) utilisent un `:` (colon) comme séparateur dans ASP.NET noms clés de configuration de [base.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="a0ade-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="a0ade-129">Le Secret Manager est utilisé à partir d’une coque `{SECRET NAME}` de commande `{SECRET VALUE}` ouverte à la racine de [contenu](xref:fundamentals/index#content-root)du projet , où est le nom et est la valeur:</span><span class="sxs-lookup"><span data-stu-id="a0ade-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="a0ade-130">Exécutez les commandes suivantes dans une coque de commande à partir de la racine de [contenu](xref:fundamentals/index#content-root) du projet pour définir les secrets de l’application d’échantillon :</span><span class="sxs-lookup"><span data-stu-id="a0ade-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="a0ade-131">Lorsque ces secrets sont stockés dans Azure Key Vault dans le [stockage Secret dans l’environnement de production avec la section Azure Key Vault,](#secret-storage-in-the-production-environment-with-azure-key-vault) le `_dev` suffixe est changé en `_prod`.</span><span class="sxs-lookup"><span data-stu-id="a0ade-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="a0ade-132">Le suffixe fournit un indice visuel dans la sortie de l’application indiquant la source des valeurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0ade-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="a0ade-133">Stockage secret dans l’environnement de production avec Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0ade-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="a0ade-134">Les instructions fournies par le [Quickstart: Set et récupérer un secret à partir d’Azure Key Vault à l’aide du sujet Azure CLI](/azure/key-vault/quick-create-cli) sont résumées ici pour la création d’un coffre-fort Azure et le stockage des secrets utilisés par l’application échantillon.</span><span class="sxs-lookup"><span data-stu-id="a0ade-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="a0ade-135">Consultez le sujet pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="a0ade-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="a0ade-136">Ouvrez la coque Azure Cloud en utilisant l’une des méthodes suivantes dans le [portail Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="a0ade-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="a0ade-137">Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code.</span><span class="sxs-lookup"><span data-stu-id="a0ade-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="a0ade-138">Utilisez la chaîne de recherche "Azure CLI" dans la boîte à texte.</span><span class="sxs-lookup"><span data-stu-id="a0ade-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="a0ade-139">Ouvrez Cloud Shell dans votre navigateur avec le bouton **Launch Cloud Shell.**</span><span class="sxs-lookup"><span data-stu-id="a0ade-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="a0ade-140">Sélectionnez le bouton **Cloud Shell** sur le menu dans le coin supérieur droit du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="a0ade-141">Pour plus d’informations, voir [Azure CLI](/cli/azure/) et [Aperçu d’Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="a0ade-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="a0ade-142">Si vous n’êtes pas déjà authentifié, connectez-vous avec la `az login` commande.</span><span class="sxs-lookup"><span data-stu-id="a0ade-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="a0ade-143">Créer un groupe de ressources `{RESOURCE GROUP NAME}` avec la commande suivante, où `{LOCATION}` est le nom du groupe de ressources pour le nouveau groupe de ressources et est la région Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="a0ade-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a0ade-144">Créer une voûte clé dans le groupe `{KEY VAULT NAME}` de ressources avec la commande `{LOCATION}` suivante, où est le nom pour la nouvelle voûte clé et est la région Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="a0ade-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a0ade-145">Créez des secrets dans la chambre forte comme paires de nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="a0ade-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="a0ade-146">Les noms secrets Azure Key Vault sont limités aux caractères alphanumériques et aux tirets.</span><span class="sxs-lookup"><span data-stu-id="a0ade-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="a0ade-147">Les valeurs hiérarchiques (sections de configuration) utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="a0ade-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a0ade-148">Les colons, qui sont normalement utilisés pour délimiter une section à partir d’un sous-clé dans [ASP.NET configuration Core](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms secrets de voûte clé.</span><span class="sxs-lookup"><span data-stu-id="a0ade-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="a0ade-149">Par conséquent, deux tirets sont utilisés et échangés contre un côlon lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="a0ade-150">Les secrets suivants sont à utiliser avec l’application échantillon.</span><span class="sxs-lookup"><span data-stu-id="a0ade-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="a0ade-151">Les valeurs `_prod` comprennent un suffixe `_dev` pour les distinguer des valeurs de suffixe chargées dans l’environnement de développement des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a0ade-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="a0ade-152">Remplacez-le `{KEY VAULT NAME}` par le nom de la voûte clé que vous avez créée dans l’étape précédente :</span><span class="sxs-lookup"><span data-stu-id="a0ade-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="a0ade-153">Utilisez l’ID d’application et le certificat X.509 pour les applications non hébergées par Azure</span><span class="sxs-lookup"><span data-stu-id="a0ade-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="a0ade-154">Configurez Azure AD, Azure Key Vault et l’application pour utiliser un identifiant d’application d’annuaire actif Azure et un certificat X.509 pour vous authentifier à un coffre-fort **clé lorsque l’application est hébergée en dehors d’Azure.**</span><span class="sxs-lookup"><span data-stu-id="a0ade-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="a0ade-155">Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="a0ade-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="a0ade-156">Bien que l’utilisation d’un ID d’application et d’un certificat X.509 soit pris en charge pour les applications hébergées à Azure, nous vous recommandons d’utiliser [les identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) lors de l’hébergement d’une application à Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="a0ade-157">Les identités gérées ne nécessitent pas de stockage d’un certificat dans l’application ou dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="a0ade-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="a0ade-158">L’application d’échantillon utilise un ID d’application `#define` et un certificat X.509 `Certificate`lorsque la déclaration en haut du fichier *Program.cs* est définie .</span><span class="sxs-lookup"><span data-stu-id="a0ade-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="a0ade-159">Créez un certificat d’archive PKCS-12 *(.pfx).*</span><span class="sxs-lookup"><span data-stu-id="a0ade-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="a0ade-160">Les options pour créer des certificats incluent [MakeCert sur Windows](/windows/desktop/seccrypto/makecert) et [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="a0ade-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="a0ade-161">Installez le certificat dans le magasin de certificats personnels de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="a0ade-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="a0ade-162">Le marquage de la clé comme exportable est facultatif.</span><span class="sxs-lookup"><span data-stu-id="a0ade-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="a0ade-163">Notez l’empreinte du pouce du certificat, qui est utilisé plus tard dans ce processus.</span><span class="sxs-lookup"><span data-stu-id="a0ade-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="a0ade-164">Exporter le certificat d’archive D’archives PKCS 12 (*.pfx*) sous forme de certificat codé par DER (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="a0ade-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="a0ade-165">Enregistrez l’application avec Azure AD **(enregistrements d’applications**).</span><span class="sxs-lookup"><span data-stu-id="a0ade-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="a0ade-166">Téléchargez le certificat codé DER (*.cer*) sur Azure AD :</span><span class="sxs-lookup"><span data-stu-id="a0ade-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="a0ade-167">Sélectionnez l’application dans Azure AD.</span><span class="sxs-lookup"><span data-stu-id="a0ade-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="a0ade-168">Naviguez vers **les certificats & secrets**.</span><span class="sxs-lookup"><span data-stu-id="a0ade-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="a0ade-169">Sélectionnez **le certificat De téléchargement** pour télécharger le certificat, qui contient la clé publique.</span><span class="sxs-lookup"><span data-stu-id="a0ade-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="a0ade-170">Un *certificat .cer*, *.pem*, ou *.crt* est acceptable.</span><span class="sxs-lookup"><span data-stu-id="a0ade-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="a0ade-171">Stockez le nom de coffre-fort, l’id d’application et l’empreinte de pouce de certificat dans le fichier *appsettings.json* de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="a0ade-172">Naviguez vers **les voûtes Key** dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="a0ade-173">Sélectionnez la voûte clé que vous avez créée dans le [stockage Secret dans l’environnement Production avec la section Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)</span><span class="sxs-lookup"><span data-stu-id="a0ade-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="a0ade-174">Sélectionnez les **politiques d’accès**.</span><span class="sxs-lookup"><span data-stu-id="a0ade-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="a0ade-175">Sélectionnez **Ajouter la politique d’accès**.</span><span class="sxs-lookup"><span data-stu-id="a0ade-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="a0ade-176">Ouvrez **les autorisations secrètes** et fournissez à l’application les autorisations **Get** and **List.**</span><span class="sxs-lookup"><span data-stu-id="a0ade-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="a0ade-177">Sélectionnez **Select principal** et sélectionnez l’application enregistrée par nom.</span><span class="sxs-lookup"><span data-stu-id="a0ade-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="a0ade-178">Sélectionnez le bouton **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="a0ade-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="a0ade-179">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="a0ade-179">Select **OK**.</span></span>
1. <span data-ttu-id="a0ade-180">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="a0ade-180">Select **Save**.</span></span>
1. <span data-ttu-id="a0ade-181">Déployez l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-181">Deploy the app.</span></span>

<span data-ttu-id="a0ade-182">L’application `Certificate` d’échantillon obtient `IConfigurationRoot` ses valeurs de configuration du même nom que le nom secret :</span><span class="sxs-lookup"><span data-stu-id="a0ade-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="a0ade-183">Valeurs non hiérarchiques : `SecretName` La valeur `config["SecretName"]`pour est obtenue avec .</span><span class="sxs-lookup"><span data-stu-id="a0ade-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="a0ade-184">Valeurs hiérarchiques (sections) : `:` Utilisation `GetSection` (colon) notation ou méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="a0ade-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="a0ade-185">Utilisez l’une ou l’autre de ces approches pour obtenir la valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="a0ade-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="a0ade-186">Le certificat X.509 est géré par le système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="a0ade-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="a0ade-187">L’application <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> appelle avec des valeurs fournies par le fichier *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="a0ade-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="a0ade-188">Exemples de valeurs :</span><span class="sxs-lookup"><span data-stu-id="a0ade-188">Example values:</span></span>

* <span data-ttu-id="a0ade-189">Nom de voûte clé:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="a0ade-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="a0ade-190">ID d’application :`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="a0ade-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="a0ade-191">Empreinte de pouce du certificat :`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="a0ade-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="a0ade-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a0ade-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="a0ade-193">Lorsque vous exécutez l’application, une page Web affiche les valeurs secrètes chargées.</span><span class="sxs-lookup"><span data-stu-id="a0ade-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a0ade-194">Dans l’environnement de développement, `_dev` les valeurs secrètes se chargent avec le suffixe.</span><span class="sxs-lookup"><span data-stu-id="a0ade-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="a0ade-195">Dans l’environnement de production, `_prod` les valeurs se chargent avec le suffixe.</span><span class="sxs-lookup"><span data-stu-id="a0ade-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="a0ade-196">Utiliser des identités gérées pour les ressources Azure</span><span class="sxs-lookup"><span data-stu-id="a0ade-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="a0ade-197">**Une application déployée sur Azure** peut tirer parti des [identités gérées pour les ressources Azure,](/azure/active-directory/managed-identities-azure-resources/overview)ce qui permet à l’application de s’authentifier avec Azure Key Vault à l’aide d’authentification Azure AD sans informations d’identification (Id d’application et Mot de Passe/Client Secret) stockée dans l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="a0ade-198">L’application d’échantillon utilise des `#define` identités gérées pour les ressources Azure `Managed`lorsque la déclaration en haut du fichier *Program.cs* est définie à .</span><span class="sxs-lookup"><span data-stu-id="a0ade-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="a0ade-199">Entrez le nom de coffre-fort dans le fichier *appsettings.json* de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="a0ade-200">L’application d’échantillon ne nécessite pas d’identifiant d’application et de mot de passe (Secret client) lorsqu’elle est configurée sur la `Managed` version, de sorte que vous pouvez ignorer ces entrées de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0ade-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="a0ade-201">L’application est déployée sur Azure, et Azure authentifie l’application pour accéder à Azure Key Vault uniquement en utilisant le nom de voûte stocké dans le fichier *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="a0ade-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="a0ade-202">Déployez l’application d’échantillon au service d’application Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="a0ade-203">Une application déployée sur Azure App Service est automatiquement enregistrée auprès d’Azure AD lors de la création du service.</span><span class="sxs-lookup"><span data-stu-id="a0ade-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="a0ade-204">Obtenez l’identifiant d’objet du déploiement pour une utilisation dans la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="a0ade-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="a0ade-205">L’identifiant d’objet est affiché dans le portail Azure sur le panneau **d’identité** du service d’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="a0ade-206">En utilisant Azure CLI et l’identifiant d’objet de l’application, fournissez à l’application `list` et `get` autorisez-vous à accéder à la voûte clé :</span><span class="sxs-lookup"><span data-stu-id="a0ade-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="a0ade-207">**Redémarrez l’application** à l’aide d’Azure CLI, PowerShell ou du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="a0ade-208">L’application échantillon:</span><span class="sxs-lookup"><span data-stu-id="a0ade-208">The sample app:</span></span>

* <span data-ttu-id="a0ade-209">Crée une instance `AzureServiceTokenProvider` de la classe sans chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="a0ade-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="a0ade-210">Lorsqu’une chaîne de connexion n’est pas fournie, le fournisseur tente d’obtenir un jeton d’identités gérées pour les ressources Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="a0ade-211">Un <xref:Microsoft.Azure.KeyVault.KeyVaultClient> nouveau est `AzureServiceTokenProvider` créé avec l’exemple rappel symbolique.</span><span class="sxs-lookup"><span data-stu-id="a0ade-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="a0ade-212">L’instance <xref:Microsoft.Azure.KeyVault.KeyVaultClient> est utilisée avec <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> une implémentation par défaut qui`--`charge toutes les`:`valeurs secrètes et remplace les doubles-dashes () par des colons ( ) dans les noms clés.</span><span class="sxs-lookup"><span data-stu-id="a0ade-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="a0ade-213">Valeur de l’exemple du nom de coffre-fort :`contosovault`</span><span class="sxs-lookup"><span data-stu-id="a0ade-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="a0ade-214">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a0ade-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="a0ade-215">Lorsque vous exécutez l’application, une page Web affiche les valeurs secrètes chargées.</span><span class="sxs-lookup"><span data-stu-id="a0ade-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a0ade-216">Dans l’environnement de développement, les valeurs secrètes ont le `_dev` suffixe parce qu’elles sont fournies par les secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a0ade-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="a0ade-217">Dans l’environnement de production, `_prod` les valeurs se chargent avec le suffixe car ils sont fournis par Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0ade-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="a0ade-218">Si vous `Access denied` recevez une erreur, confirmez que l’application est enregistrée auprès d’Azure AD et avez donné accès au coffre-fort.</span><span class="sxs-lookup"><span data-stu-id="a0ade-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="a0ade-219">Confirmez que vous avez redémarré le service à Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="a0ade-220">Pour plus d’informations sur l’utilisation du fournisseur avec une identité gérée et un pipeline Azure DevOps, voir [Créer une connexion de service Azure Resource Manager à une VM avec une identité de service gérée](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="a0ade-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="a0ade-221">Options de configuration</span><span class="sxs-lookup"><span data-stu-id="a0ade-221">Configuration options</span></span>

<span data-ttu-id="a0ade-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>peut accepter <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>un :</span><span class="sxs-lookup"><span data-stu-id="a0ade-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="a0ade-223">Propriété</span><span class="sxs-lookup"><span data-stu-id="a0ade-223">Property</span></span>         | <span data-ttu-id="a0ade-224">Description</span><span class="sxs-lookup"><span data-stu-id="a0ade-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="a0ade-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>à utiliser pour récupérer les valeurs.</span><span class="sxs-lookup"><span data-stu-id="a0ade-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="a0ade-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>instance utilisée pour contrôler le chargement secret.</span><span class="sxs-lookup"><span data-stu-id="a0ade-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="a0ade-227">`Timespan`d’attendre entre les tentatives de sondage de la voûte clé pour les changements.</span><span class="sxs-lookup"><span data-stu-id="a0ade-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="a0ade-228">La valeur `null` par défaut est (la configuration n’est pas rechargée).</span><span class="sxs-lookup"><span data-stu-id="a0ade-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="a0ade-229">Clé voûte URI.</span><span class="sxs-lookup"><span data-stu-id="a0ade-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="a0ade-230">Utilisez un préfixe de nom clé</span><span class="sxs-lookup"><span data-stu-id="a0ade-230">Use a key name prefix</span></span>

<span data-ttu-id="a0ade-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fournit une surcharge qui accepte <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>une implémentation de , qui vous permet de contrôler la façon dont les secrets de voûte clés sont convertis en clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0ade-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="a0ade-232">Par exemple, vous pouvez implémenter l’interface pour charger des valeurs secrètes en fonction d’une valeur préfixe que vous fournissez lors du démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="a0ade-233">Cela vous permet, par exemple, de charger des secrets basés sur la version de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="a0ade-234">N’utilisez pas de préfixes sur les secrets clés de voûte pour placer des secrets pour plusieurs applications dans la même voûte clé ou pour placer des secrets environnementaux (par exemple, *le développement* contre les secrets *de production)* dans la même voûte.</span><span class="sxs-lookup"><span data-stu-id="a0ade-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="a0ade-235">Nous recommandons que différentes applications et environnements de développement/production utilisent des coffres-forts distincts pour isoler les environnements d’applications pour le plus haut niveau de sécurité.</span><span class="sxs-lookup"><span data-stu-id="a0ade-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="a0ade-236">Dans l’exemple suivant, un secret est établi dans la chambre forte (et en utilisant l’outil Secret Manager pour l’environnement de développement) pour `5000-AppSecret` (les périodes ne sont pas autorisées dans les noms secrets de voûte clé).</span><span class="sxs-lookup"><span data-stu-id="a0ade-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="a0ade-237">Ce secret représente un secret d’application pour la version 5.0.0.0 de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="a0ade-238">Pour une autre version de l’application, 5.1.0.0, un secret est ajouté `5100-AppSecret`à la voûte clé (et en utilisant l’outil Secret Manager) pour .</span><span class="sxs-lookup"><span data-stu-id="a0ade-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="a0ade-239">Chaque version d’application charge sa valeur `AppSecret`secrète versionnée dans sa configuration comme, dépouillant la version pendant qu’elle charge le secret.</span><span class="sxs-lookup"><span data-stu-id="a0ade-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="a0ade-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>est appelé avec <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>une coutume :</span><span class="sxs-lookup"><span data-stu-id="a0ade-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="a0ade-241">La <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> mise en œuvre réagit aux préfixes de la version des secrets pour charger le secret approprié dans la configuration:</span><span class="sxs-lookup"><span data-stu-id="a0ade-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="a0ade-242">`Load`charge un secret lorsque son nom commence par le préfixe.</span><span class="sxs-lookup"><span data-stu-id="a0ade-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="a0ade-243">D’autres secrets ne sont pas chargés.</span><span class="sxs-lookup"><span data-stu-id="a0ade-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="a0ade-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="a0ade-244">`GetKey`:</span></span>
  * <span data-ttu-id="a0ade-245">Supprime le préfixe du nom secret.</span><span class="sxs-lookup"><span data-stu-id="a0ade-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="a0ade-246">Remplace deux tirets dans n’importe quel nom avec le `KeyDelimiter`, qui est le délimital utilisé en configuration (généralement un côlon).</span><span class="sxs-lookup"><span data-stu-id="a0ade-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="a0ade-247">Azure Key Vault ne permet pas un côlon dans des noms secrets.</span><span class="sxs-lookup"><span data-stu-id="a0ade-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="a0ade-248">La `Load` méthode est appelée par un algorithme de fournisseur qui itère à travers les secrets de voûte pour trouver ceux qui ont le préfixe de version.</span><span class="sxs-lookup"><span data-stu-id="a0ade-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="a0ade-249">Lorsqu’un préfixe `Load`de version est `GetKey` trouvé avec, l’algorithme utilise la méthode pour retourner le nom de configuration du nom secret.</span><span class="sxs-lookup"><span data-stu-id="a0ade-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="a0ade-250">Il dépouille le préfixe de la version du nom du secret et renvoie le reste du nom secret pour le chargement dans les paires de nom de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="a0ade-251">Lorsque cette approche est mise en œuvre :</span><span class="sxs-lookup"><span data-stu-id="a0ade-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="a0ade-252">La version de l’application spécifiée dans le fichier de projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="a0ade-253">Dans l’exemple suivant, la version `5.0.0.0`de l’application est définie pour :</span><span class="sxs-lookup"><span data-stu-id="a0ade-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="a0ade-254">Confirmez `<UserSecretsId>` qu’une propriété est présente dans `{GUID}` le fichier de projet de l’application, où se trouve un GUID fourni par l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="a0ade-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="a0ade-255">Enregistrez les secrets suivants localement avec [l’outil Secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="a0ade-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="a0ade-256">Les secrets sont enregistrés dans Azure Key Vault à l’aide des commandes Azure CLI suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0ade-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="a0ade-257">Lorsque l’application est exécutée, les secrets de voûte clés sont chargés.</span><span class="sxs-lookup"><span data-stu-id="a0ade-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="a0ade-258">Le secret `5000-AppSecret` de la chaîne est adapté à la version de`5.0.0.0`l’application spécifiée dans le fichier de projet de l’application ( ).</span><span class="sxs-lookup"><span data-stu-id="a0ade-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="a0ade-259">La version, `5000` (avec le tableau de bord), est dépouillée du nom clé.</span><span class="sxs-lookup"><span data-stu-id="a0ade-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="a0ade-260">Tout au long de l’application, la configuration de lecture avec la clé `AppSecret` charge la valeur secrète.</span><span class="sxs-lookup"><span data-stu-id="a0ade-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="a0ade-261">Si la version de l’application est `5.1.0.0` modifiée dans le fichier du projet `5.1.0.0_secret_value_dev` et que `5.1.0.0_secret_value_prod` l’application est exécutée à nouveau, la valeur secrète retournée est dans l’environnement de développement et dans la production.</span><span class="sxs-lookup"><span data-stu-id="a0ade-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="a0ade-262">Vous pouvez également <xref:Microsoft.Azure.KeyVault.KeyVaultClient> fournir <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>votre propre implémentation à .</span><span class="sxs-lookup"><span data-stu-id="a0ade-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="a0ade-263">Un client personnalisé permet de partager une seule instance du client à travers l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a0ade-264">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="a0ade-264">Bind an array to a class</span></span>

<span data-ttu-id="a0ade-265">Le fournisseur est capable de lire les valeurs de configuration dans un tableau de liaison à un tableau POCO.</span><span class="sxs-lookup"><span data-stu-id="a0ade-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="a0ade-266">Lors de la lecture à partir d’une source de configuration qui permet aux touches de contenir des séparateurs du côlon (`:`) une clé`:0:`numérique est utilisée pour distinguer les touches qui composent un tableau ( , `:1:`. &hellip; `:{n}:`. .</span><span class="sxs-lookup"><span data-stu-id="a0ade-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="a0ade-267">Pour plus d’informations, voir [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="a0ade-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="a0ade-268">Les touches Azure Key Vault ne peuvent pas utiliser un côlon comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="a0ade-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="a0ade-269">L’approche décrite dans ce sujet`--`utilise double tirets ( ) comme séparateur de valeurs hiérarchiques (sections).</span><span class="sxs-lookup"><span data-stu-id="a0ade-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="a0ade-270">Les touches de tableau sont stockées dans Azure`--0--`Key `--1--` &hellip; `--{n}--`Vault avec des tirets doubles et des segments de clés numériques ( , . . .</span><span class="sxs-lookup"><span data-stu-id="a0ade-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="a0ade-271">Examinez la configuration suivante du fournisseur d’enregistrement [Serilog](https://serilog.net/) fournie par un fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="a0ade-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="a0ade-272">Il y a deux littérals d’objet définis dans le `WriteTo` tableau qui reflètent deux *éviers*de Serilog, qui décrivent des destinations pour la sortie d’exploitation forestière :</span><span class="sxs-lookup"><span data-stu-id="a0ade-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="a0ade-273">La configuration indiquée dans le fichier précédent JSON est`--`stockée dans Azure Key Vault à l’aide de doubles segments de tableau de bord () et numériques :</span><span class="sxs-lookup"><span data-stu-id="a0ade-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="a0ade-274">Clé</span><span class="sxs-lookup"><span data-stu-id="a0ade-274">Key</span></span> | <span data-ttu-id="a0ade-275">Valeur</span><span class="sxs-lookup"><span data-stu-id="a0ade-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="a0ade-276">Recharger les secrets</span><span class="sxs-lookup"><span data-stu-id="a0ade-276">Reload secrets</span></span>

<span data-ttu-id="a0ade-277">Les secrets sont `IConfigurationRoot.Reload()` mis en cache jusqu’à ce qu’on l’appelle.</span><span class="sxs-lookup"><span data-stu-id="a0ade-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="a0ade-278">Les secrets périmés, désactivés et mis à `Reload` jour dans le coffre-fort n’ont pas été respectés par l’application tant qu’ils ne sont pas exécutés.</span><span class="sxs-lookup"><span data-stu-id="a0ade-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="a0ade-279">Secrets handicapés et expirés</span><span class="sxs-lookup"><span data-stu-id="a0ade-279">Disabled and expired secrets</span></span>

<span data-ttu-id="a0ade-280">Les secrets handicapés <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>et expirés jettent un .</span><span class="sxs-lookup"><span data-stu-id="a0ade-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="a0ade-281">Pour empêcher l’application de lancer, fournissez la configuration à l’aide d’un fournisseur de configuration différent ou mettez à jour le secret désactivé ou expiré.</span><span class="sxs-lookup"><span data-stu-id="a0ade-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a0ade-282">Dépanner</span><span class="sxs-lookup"><span data-stu-id="a0ade-282">Troubleshoot</span></span>

<span data-ttu-id="a0ade-283">Lorsque l’application ne charge pas la configuration à l’aide du fournisseur, un message d’erreur est écrit à [l’infrastructure ASP.NET Core Logging](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a0ade-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a0ade-284">Les conditions suivantes empêcheront la configuration de charger :</span><span class="sxs-lookup"><span data-stu-id="a0ade-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="a0ade-285">L’application ou le certificat n’est pas configuré correctement dans Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a0ade-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="a0ade-286">La voûte clé n’existe pas dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0ade-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="a0ade-287">L’application n’est pas autorisée à accéder à la voûte clé.</span><span class="sxs-lookup"><span data-stu-id="a0ade-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="a0ade-288">La politique d’accès `Get` `List` n’inclut pas et les autorisations.</span><span class="sxs-lookup"><span data-stu-id="a0ade-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="a0ade-289">Dans le coffre-fort, les données de configuration (paire de nom) sont incorrectement nommées, manquantes, désactivées ou expirées.</span><span class="sxs-lookup"><span data-stu-id="a0ade-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="a0ade-290">L’application a le mauvais`KeyVaultName`nom de coffre-fort`AzureADApplicationId`de clé ( ), Azure AD Application Id ( ), ou Azure AD certificat thumbprint (`AzureADCertThumbprint`).</span><span class="sxs-lookup"><span data-stu-id="a0ade-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="a0ade-291">La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.</span><span class="sxs-lookup"><span data-stu-id="a0ade-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="a0ade-292">Lors de l’ajout de la stratégie d’accès de l’application à la chambre forte, la stratégie a été créée, mais le bouton **Enregistrer** n’a pas été sélectionné dans l’interface utilisateur **des stratégies d’accès.**</span><span class="sxs-lookup"><span data-stu-id="a0ade-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0ade-293">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a0ade-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="a0ade-294">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0ade-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="a0ade-295">Microsoft Azure: Key Vault Documentation</span><span class="sxs-lookup"><span data-stu-id="a0ade-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="a0ade-296">Comment générer et transférer des clés protégées par HSM pour Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0ade-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="a0ade-297">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="a0ade-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="a0ade-298">Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET</span><span class="sxs-lookup"><span data-stu-id="a0ade-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="a0ade-299">Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Windows Azure et le langage .NET</span><span class="sxs-lookup"><span data-stu-id="a0ade-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a0ade-300">Ce document explique comment utiliser le fournisseur [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Pour charger les valeurs de configuration des applications à partir des secrets Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0ade-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="a0ade-301">Azure Key Vault est un service basé sur le cloud qui aide à protéger les clés cryptographiques et les secrets utilisés par les applications et les services.</span><span class="sxs-lookup"><span data-stu-id="a0ade-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="a0ade-302">Les scénarios courants pour l’utilisation d’Azure Key Vault avec des applications ASP.NET Core incluent :</span><span class="sxs-lookup"><span data-stu-id="a0ade-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="a0ade-303">Contrôle de l’accès aux données de configuration sensibles.</span><span class="sxs-lookup"><span data-stu-id="a0ade-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="a0ade-304">Répondre à l’exigence de FIPS 140-2 Niveau 2 validé modules de sécurité matérielle (HSM) lors du stockage des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0ade-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="a0ade-305">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a0ade-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="a0ade-306">.</span><span class="sxs-lookup"><span data-stu-id="a0ade-306">Packages</span></span>

<span data-ttu-id="a0ade-307">Ajoutez une référence de paquet au package [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)</span><span class="sxs-lookup"><span data-stu-id="a0ade-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="a0ade-308">Exemple d'application</span><span class="sxs-lookup"><span data-stu-id="a0ade-308">Sample app</span></span>

<span data-ttu-id="a0ade-309">L’application d’échantillon s’exécute `#define` dans l’un des deux modes déterminés par la déclaration en haut du fichier *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="a0ade-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="a0ade-310">`Certificate`&ndash; Démontre l’utilisation d’un id De client Azure Key Vault et d’un certificat X.509 pour accéder aux secrets stockés dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0ade-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="a0ade-311">Cette version de l’échantillon peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou tout hôte capable de servir une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a0ade-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="a0ade-312">`Managed`&ndash; Démontre comment utiliser [les identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l’application à Azure Key Vault avec l’authentification Azure AD sans informations d’identification stockées dans le code ou la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="a0ade-313">Lors de l’utilisation d’identités gérées pour authentifier, un ID d’application Azure AD et mot de passe (Secret client) ne sont pas nécessaires.</span><span class="sxs-lookup"><span data-stu-id="a0ade-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="a0ade-314">La `Managed` version de l’échantillon doit être déployée à Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="a0ade-315">Suivez les conseils dans la section [Utiliser les identités gérées pour les ressources Azure.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="a0ade-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="a0ade-316">Pour plus d’informations sur la façon de`#define`configurer <xref:index#preprocessor-directives-in-sample-code>une application d’échantillon à l’aide de directives de préprocesseur ( ), voir .</span><span class="sxs-lookup"><span data-stu-id="a0ade-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="a0ade-317">Stockage secret dans l’environnement de développement</span><span class="sxs-lookup"><span data-stu-id="a0ade-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="a0ade-318">Définissez des secrets localement à l’aide de [l’outil Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a0ade-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a0ade-319">Lorsque l’application d’échantillon s’exécute sur la machine locale dans l’environnement de développement, les secrets sont chargés à partir du magasin secret manager local.</span><span class="sxs-lookup"><span data-stu-id="a0ade-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="a0ade-320">L’outil Secret `<UserSecretsId>` Manager nécessite une propriété dans le fichier de projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="a0ade-321">Définissez la`{GUID}`valeur de la propriété ( ) à n’importe quel GUID unique :</span><span class="sxs-lookup"><span data-stu-id="a0ade-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="a0ade-322">Les secrets sont créés comme paires de valeurs nom.</span><span class="sxs-lookup"><span data-stu-id="a0ade-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="a0ade-323">Les valeurs hiérarchiques (sections de configuration) utilisent un `:` (colon) comme séparateur dans ASP.NET noms clés de configuration de [base.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="a0ade-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="a0ade-324">Le Secret Manager est utilisé à partir d’une coque `{SECRET NAME}` de commande `{SECRET VALUE}` ouverte à la racine de [contenu](xref:fundamentals/index#content-root)du projet , où est le nom et est la valeur:</span><span class="sxs-lookup"><span data-stu-id="a0ade-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="a0ade-325">Exécutez les commandes suivantes dans une coque de commande à partir de la racine de [contenu](xref:fundamentals/index#content-root) du projet pour définir les secrets de l’application d’échantillon :</span><span class="sxs-lookup"><span data-stu-id="a0ade-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="a0ade-326">Lorsque ces secrets sont stockés dans Azure Key Vault dans le [stockage Secret dans l’environnement de production avec la section Azure Key Vault,](#secret-storage-in-the-production-environment-with-azure-key-vault) le `_dev` suffixe est changé en `_prod`.</span><span class="sxs-lookup"><span data-stu-id="a0ade-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="a0ade-327">Le suffixe fournit un indice visuel dans la sortie de l’application indiquant la source des valeurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0ade-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="a0ade-328">Stockage secret dans l’environnement de production avec Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0ade-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="a0ade-329">Les instructions fournies par le [Quickstart: Set et récupérer un secret à partir d’Azure Key Vault à l’aide du sujet Azure CLI](/azure/key-vault/quick-create-cli) sont résumées ici pour la création d’un coffre-fort Azure et le stockage des secrets utilisés par l’application échantillon.</span><span class="sxs-lookup"><span data-stu-id="a0ade-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="a0ade-330">Consultez le sujet pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="a0ade-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="a0ade-331">Ouvrez la coque Azure Cloud en utilisant l’une des méthodes suivantes dans le [portail Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="a0ade-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="a0ade-332">Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code.</span><span class="sxs-lookup"><span data-stu-id="a0ade-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="a0ade-333">Utilisez la chaîne de recherche "Azure CLI" dans la boîte à texte.</span><span class="sxs-lookup"><span data-stu-id="a0ade-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="a0ade-334">Ouvrez Cloud Shell dans votre navigateur avec le bouton **Launch Cloud Shell.**</span><span class="sxs-lookup"><span data-stu-id="a0ade-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="a0ade-335">Sélectionnez le bouton **Cloud Shell** sur le menu dans le coin supérieur droit du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="a0ade-336">Pour plus d’informations, voir [Azure CLI](/cli/azure/) et [Aperçu d’Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="a0ade-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="a0ade-337">Si vous n’êtes pas déjà authentifié, connectez-vous avec la `az login` commande.</span><span class="sxs-lookup"><span data-stu-id="a0ade-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="a0ade-338">Créer un groupe de ressources `{RESOURCE GROUP NAME}` avec la commande suivante, où `{LOCATION}` est le nom du groupe de ressources pour le nouveau groupe de ressources et est la région Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="a0ade-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a0ade-339">Créer une voûte clé dans le groupe `{KEY VAULT NAME}` de ressources avec la commande `{LOCATION}` suivante, où est le nom pour la nouvelle voûte clé et est la région Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="a0ade-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a0ade-340">Créez des secrets dans la chambre forte comme paires de nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="a0ade-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="a0ade-341">Les noms secrets Azure Key Vault sont limités aux caractères alphanumériques et aux tirets.</span><span class="sxs-lookup"><span data-stu-id="a0ade-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="a0ade-342">Les valeurs hiérarchiques (sections de configuration) utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="a0ade-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a0ade-343">Les colons, qui sont normalement utilisés pour délimiter une section à partir d’un sous-clé dans [ASP.NET configuration Core](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms secrets de voûte clé.</span><span class="sxs-lookup"><span data-stu-id="a0ade-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="a0ade-344">Par conséquent, deux tirets sont utilisés et échangés contre un côlon lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="a0ade-345">Les secrets suivants sont à utiliser avec l’application échantillon.</span><span class="sxs-lookup"><span data-stu-id="a0ade-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="a0ade-346">Les valeurs `_prod` comprennent un suffixe `_dev` pour les distinguer des valeurs de suffixe chargées dans l’environnement de développement des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a0ade-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="a0ade-347">Remplacez-le `{KEY VAULT NAME}` par le nom de la voûte clé que vous avez créée dans l’étape précédente :</span><span class="sxs-lookup"><span data-stu-id="a0ade-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="a0ade-348">Utilisez l’ID d’application et le certificat X.509 pour les applications non hébergées par Azure</span><span class="sxs-lookup"><span data-stu-id="a0ade-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="a0ade-349">Configurez Azure AD, Azure Key Vault et l’application pour utiliser un identifiant d’application d’annuaire actif Azure et un certificat X.509 pour vous authentifier à un coffre-fort **clé lorsque l’application est hébergée en dehors d’Azure.**</span><span class="sxs-lookup"><span data-stu-id="a0ade-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="a0ade-350">Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="a0ade-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="a0ade-351">Bien que l’utilisation d’un ID d’application et d’un certificat X.509 soit pris en charge pour les applications hébergées à Azure, nous vous recommandons d’utiliser [les identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) lors de l’hébergement d’une application à Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="a0ade-352">Les identités gérées ne nécessitent pas de stockage d’un certificat dans l’application ou dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="a0ade-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="a0ade-353">L’application d’échantillon utilise un ID d’application `#define` et un certificat X.509 `Certificate`lorsque la déclaration en haut du fichier *Program.cs* est définie .</span><span class="sxs-lookup"><span data-stu-id="a0ade-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="a0ade-354">Créez un certificat d’archive PKCS-12 *(.pfx).*</span><span class="sxs-lookup"><span data-stu-id="a0ade-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="a0ade-355">Les options pour créer des certificats incluent [MakeCert sur Windows](/windows/desktop/seccrypto/makecert) et [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="a0ade-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="a0ade-356">Installez le certificat dans le magasin de certificats personnels de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="a0ade-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="a0ade-357">Le marquage de la clé comme exportable est facultatif.</span><span class="sxs-lookup"><span data-stu-id="a0ade-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="a0ade-358">Notez l’empreinte du pouce du certificat, qui est utilisé plus tard dans ce processus.</span><span class="sxs-lookup"><span data-stu-id="a0ade-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="a0ade-359">Exporter le certificat d’archive D’archives PKCS 12 (*.pfx*) sous forme de certificat codé par DER (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="a0ade-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="a0ade-360">Enregistrez l’application avec Azure AD **(enregistrements d’applications**).</span><span class="sxs-lookup"><span data-stu-id="a0ade-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="a0ade-361">Téléchargez le certificat codé DER (*.cer*) sur Azure AD :</span><span class="sxs-lookup"><span data-stu-id="a0ade-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="a0ade-362">Sélectionnez l’application dans Azure AD.</span><span class="sxs-lookup"><span data-stu-id="a0ade-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="a0ade-363">Naviguez vers **les certificats & secrets**.</span><span class="sxs-lookup"><span data-stu-id="a0ade-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="a0ade-364">Sélectionnez **le certificat De téléchargement** pour télécharger le certificat, qui contient la clé publique.</span><span class="sxs-lookup"><span data-stu-id="a0ade-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="a0ade-365">Un *certificat .cer*, *.pem*, ou *.crt* est acceptable.</span><span class="sxs-lookup"><span data-stu-id="a0ade-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="a0ade-366">Stockez le nom de coffre-fort, l’id d’application et l’empreinte de pouce de certificat dans le fichier *appsettings.json* de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="a0ade-367">Naviguez vers **les voûtes Key** dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="a0ade-368">Sélectionnez la voûte clé que vous avez créée dans le [stockage Secret dans l’environnement Production avec la section Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)</span><span class="sxs-lookup"><span data-stu-id="a0ade-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="a0ade-369">Sélectionnez les **politiques d’accès**.</span><span class="sxs-lookup"><span data-stu-id="a0ade-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="a0ade-370">Sélectionnez **Ajouter la politique d’accès**.</span><span class="sxs-lookup"><span data-stu-id="a0ade-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="a0ade-371">Ouvrez **les autorisations secrètes** et fournissez à l’application les autorisations **Get** and **List.**</span><span class="sxs-lookup"><span data-stu-id="a0ade-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="a0ade-372">Sélectionnez **Select principal** et sélectionnez l’application enregistrée par nom.</span><span class="sxs-lookup"><span data-stu-id="a0ade-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="a0ade-373">Sélectionnez le bouton **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="a0ade-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="a0ade-374">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="a0ade-374">Select **OK**.</span></span>
1. <span data-ttu-id="a0ade-375">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="a0ade-375">Select **Save**.</span></span>
1. <span data-ttu-id="a0ade-376">Déployez l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-376">Deploy the app.</span></span>

<span data-ttu-id="a0ade-377">L’application `Certificate` d’échantillon obtient `IConfigurationRoot` ses valeurs de configuration du même nom que le nom secret :</span><span class="sxs-lookup"><span data-stu-id="a0ade-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="a0ade-378">Valeurs non hiérarchiques : `SecretName` La valeur `config["SecretName"]`pour est obtenue avec .</span><span class="sxs-lookup"><span data-stu-id="a0ade-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="a0ade-379">Valeurs hiérarchiques (sections) : `:` Utilisation `GetSection` (colon) notation ou méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="a0ade-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="a0ade-380">Utilisez l’une ou l’autre de ces approches pour obtenir la valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="a0ade-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="a0ade-381">Le certificat X.509 est géré par le système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="a0ade-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="a0ade-382">L’application <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> appelle avec des valeurs fournies par le fichier *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="a0ade-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="a0ade-383">Exemples de valeurs :</span><span class="sxs-lookup"><span data-stu-id="a0ade-383">Example values:</span></span>

* <span data-ttu-id="a0ade-384">Nom de voûte clé:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="a0ade-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="a0ade-385">ID d’application :`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="a0ade-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="a0ade-386">Empreinte de pouce du certificat :`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="a0ade-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="a0ade-387">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a0ade-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="a0ade-388">Lorsque vous exécutez l’application, une page Web affiche les valeurs secrètes chargées.</span><span class="sxs-lookup"><span data-stu-id="a0ade-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a0ade-389">Dans l’environnement de développement, `_dev` les valeurs secrètes se chargent avec le suffixe.</span><span class="sxs-lookup"><span data-stu-id="a0ade-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="a0ade-390">Dans l’environnement de production, `_prod` les valeurs se chargent avec le suffixe.</span><span class="sxs-lookup"><span data-stu-id="a0ade-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="a0ade-391">Utiliser des identités gérées pour les ressources Azure</span><span class="sxs-lookup"><span data-stu-id="a0ade-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="a0ade-392">**Une application déployée sur Azure** peut tirer parti des [identités gérées pour les ressources Azure,](/azure/active-directory/managed-identities-azure-resources/overview)ce qui permet à l’application de s’authentifier avec Azure Key Vault à l’aide d’authentification Azure AD sans informations d’identification (Id d’application et Mot de Passe/Client Secret) stockée dans l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="a0ade-393">L’application d’échantillon utilise des `#define` identités gérées pour les ressources Azure `Managed`lorsque la déclaration en haut du fichier *Program.cs* est définie à .</span><span class="sxs-lookup"><span data-stu-id="a0ade-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="a0ade-394">Entrez le nom de coffre-fort dans le fichier *appsettings.json* de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="a0ade-395">L’application d’échantillon ne nécessite pas d’identifiant d’application et de mot de passe (Secret client) lorsqu’elle est configurée sur la `Managed` version, de sorte que vous pouvez ignorer ces entrées de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0ade-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="a0ade-396">L’application est déployée sur Azure, et Azure authentifie l’application pour accéder à Azure Key Vault uniquement en utilisant le nom de voûte stocké dans le fichier *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="a0ade-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="a0ade-397">Déployez l’application d’échantillon au service d’application Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="a0ade-398">Une application déployée sur Azure App Service est automatiquement enregistrée auprès d’Azure AD lors de la création du service.</span><span class="sxs-lookup"><span data-stu-id="a0ade-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="a0ade-399">Obtenez l’identifiant d’objet du déploiement pour une utilisation dans la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="a0ade-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="a0ade-400">L’identifiant d’objet est affiché dans le portail Azure sur le panneau **d’identité** du service d’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="a0ade-401">En utilisant Azure CLI et l’identifiant d’objet de l’application, fournissez à l’application `list` et `get` autorisez-vous à accéder à la voûte clé :</span><span class="sxs-lookup"><span data-stu-id="a0ade-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="a0ade-402">**Redémarrez l’application** à l’aide d’Azure CLI, PowerShell ou du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="a0ade-403">L’application échantillon:</span><span class="sxs-lookup"><span data-stu-id="a0ade-403">The sample app:</span></span>

* <span data-ttu-id="a0ade-404">Crée une instance `AzureServiceTokenProvider` de la classe sans chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="a0ade-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="a0ade-405">Lorsqu’une chaîne de connexion n’est pas fournie, le fournisseur tente d’obtenir un jeton d’identités gérées pour les ressources Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="a0ade-406">Un <xref:Microsoft.Azure.KeyVault.KeyVaultClient> nouveau est `AzureServiceTokenProvider` créé avec l’exemple rappel symbolique.</span><span class="sxs-lookup"><span data-stu-id="a0ade-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="a0ade-407">L’instance <xref:Microsoft.Azure.KeyVault.KeyVaultClient> est utilisée avec <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> une implémentation par défaut qui`--`charge toutes les`:`valeurs secrètes et remplace les doubles-dashes () par des colons ( ) dans les noms clés.</span><span class="sxs-lookup"><span data-stu-id="a0ade-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="a0ade-408">Valeur de l’exemple du nom de coffre-fort :`contosovault`</span><span class="sxs-lookup"><span data-stu-id="a0ade-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="a0ade-409">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a0ade-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="a0ade-410">Lorsque vous exécutez l’application, une page Web affiche les valeurs secrètes chargées.</span><span class="sxs-lookup"><span data-stu-id="a0ade-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a0ade-411">Dans l’environnement de développement, les valeurs secrètes ont le `_dev` suffixe parce qu’elles sont fournies par les secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a0ade-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="a0ade-412">Dans l’environnement de production, `_prod` les valeurs se chargent avec le suffixe car ils sont fournis par Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0ade-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="a0ade-413">Si vous `Access denied` recevez une erreur, confirmez que l’application est enregistrée auprès d’Azure AD et avez donné accès au coffre-fort.</span><span class="sxs-lookup"><span data-stu-id="a0ade-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="a0ade-414">Confirmez que vous avez redémarré le service à Azure.</span><span class="sxs-lookup"><span data-stu-id="a0ade-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="a0ade-415">Pour plus d’informations sur l’utilisation du fournisseur avec une identité gérée et un pipeline Azure DevOps, voir [Créer une connexion de service Azure Resource Manager à une VM avec une identité de service gérée](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="a0ade-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="a0ade-416">Utilisez un préfixe de nom clé</span><span class="sxs-lookup"><span data-stu-id="a0ade-416">Use a key name prefix</span></span>

<span data-ttu-id="a0ade-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fournit une surcharge qui accepte <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>une implémentation de , qui vous permet de contrôler la façon dont les secrets de voûte clés sont convertis en clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0ade-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="a0ade-418">Par exemple, vous pouvez implémenter l’interface pour charger des valeurs secrètes en fonction d’une valeur préfixe que vous fournissez lors du démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="a0ade-419">Cela vous permet, par exemple, de charger des secrets basés sur la version de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="a0ade-420">N’utilisez pas de préfixes sur les secrets clés de voûte pour placer des secrets pour plusieurs applications dans la même voûte clé ou pour placer des secrets environnementaux (par exemple, *le développement* contre les secrets *de production)* dans la même voûte.</span><span class="sxs-lookup"><span data-stu-id="a0ade-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="a0ade-421">Nous recommandons que différentes applications et environnements de développement/production utilisent des coffres-forts distincts pour isoler les environnements d’applications pour le plus haut niveau de sécurité.</span><span class="sxs-lookup"><span data-stu-id="a0ade-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="a0ade-422">Dans l’exemple suivant, un secret est établi dans la chambre forte (et en utilisant l’outil Secret Manager pour l’environnement de développement) pour `5000-AppSecret` (les périodes ne sont pas autorisées dans les noms secrets de voûte clé).</span><span class="sxs-lookup"><span data-stu-id="a0ade-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="a0ade-423">Ce secret représente un secret d’application pour la version 5.0.0.0 de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="a0ade-424">Pour une autre version de l’application, 5.1.0.0, un secret est ajouté `5100-AppSecret`à la voûte clé (et en utilisant l’outil Secret Manager) pour .</span><span class="sxs-lookup"><span data-stu-id="a0ade-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="a0ade-425">Chaque version d’application charge sa valeur `AppSecret`secrète versionnée dans sa configuration comme, dépouillant la version pendant qu’elle charge le secret.</span><span class="sxs-lookup"><span data-stu-id="a0ade-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="a0ade-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>est appelé avec <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>une coutume :</span><span class="sxs-lookup"><span data-stu-id="a0ade-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="a0ade-427">La <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> mise en œuvre réagit aux préfixes de la version des secrets pour charger le secret approprié dans la configuration:</span><span class="sxs-lookup"><span data-stu-id="a0ade-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="a0ade-428">`Load`charge un secret lorsque son nom commence par le préfixe.</span><span class="sxs-lookup"><span data-stu-id="a0ade-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="a0ade-429">D’autres secrets ne sont pas chargés.</span><span class="sxs-lookup"><span data-stu-id="a0ade-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="a0ade-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="a0ade-430">`GetKey`:</span></span>
  * <span data-ttu-id="a0ade-431">Supprime le préfixe du nom secret.</span><span class="sxs-lookup"><span data-stu-id="a0ade-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="a0ade-432">Remplace deux tirets dans n’importe quel nom avec le `KeyDelimiter`, qui est le délimital utilisé en configuration (généralement un côlon).</span><span class="sxs-lookup"><span data-stu-id="a0ade-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="a0ade-433">Azure Key Vault ne permet pas un côlon dans des noms secrets.</span><span class="sxs-lookup"><span data-stu-id="a0ade-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="a0ade-434">La `Load` méthode est appelée par un algorithme de fournisseur qui itère à travers les secrets de voûte pour trouver ceux qui ont le préfixe de version.</span><span class="sxs-lookup"><span data-stu-id="a0ade-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="a0ade-435">Lorsqu’un préfixe `Load`de version est `GetKey` trouvé avec, l’algorithme utilise la méthode pour retourner le nom de configuration du nom secret.</span><span class="sxs-lookup"><span data-stu-id="a0ade-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="a0ade-436">Il dépouille le préfixe de la version du nom du secret et renvoie le reste du nom secret pour le chargement dans les paires de nom de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="a0ade-437">Lorsque cette approche est mise en œuvre :</span><span class="sxs-lookup"><span data-stu-id="a0ade-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="a0ade-438">La version de l’application spécifiée dans le fichier de projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="a0ade-439">Dans l’exemple suivant, la version `5.0.0.0`de l’application est définie pour :</span><span class="sxs-lookup"><span data-stu-id="a0ade-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="a0ade-440">Confirmez `<UserSecretsId>` qu’une propriété est présente dans `{GUID}` le fichier de projet de l’application, où se trouve un GUID fourni par l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="a0ade-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="a0ade-441">Enregistrez les secrets suivants localement avec [l’outil Secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="a0ade-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="a0ade-442">Les secrets sont enregistrés dans Azure Key Vault à l’aide des commandes Azure CLI suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0ade-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="a0ade-443">Lorsque l’application est exécutée, les secrets de voûte clés sont chargés.</span><span class="sxs-lookup"><span data-stu-id="a0ade-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="a0ade-444">Le secret `5000-AppSecret` de la chaîne est adapté à la version de`5.0.0.0`l’application spécifiée dans le fichier de projet de l’application ( ).</span><span class="sxs-lookup"><span data-stu-id="a0ade-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="a0ade-445">La version, `5000` (avec le tableau de bord), est dépouillée du nom clé.</span><span class="sxs-lookup"><span data-stu-id="a0ade-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="a0ade-446">Tout au long de l’application, la configuration de lecture avec la clé `AppSecret` charge la valeur secrète.</span><span class="sxs-lookup"><span data-stu-id="a0ade-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="a0ade-447">Si la version de l’application est `5.1.0.0` modifiée dans le fichier du projet `5.1.0.0_secret_value_dev` et que `5.1.0.0_secret_value_prod` l’application est exécutée à nouveau, la valeur secrète retournée est dans l’environnement de développement et dans la production.</span><span class="sxs-lookup"><span data-stu-id="a0ade-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="a0ade-448">Vous pouvez également <xref:Microsoft.Azure.KeyVault.KeyVaultClient> fournir <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>votre propre implémentation à .</span><span class="sxs-lookup"><span data-stu-id="a0ade-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="a0ade-449">Un client personnalisé permet de partager une seule instance du client à travers l’application.</span><span class="sxs-lookup"><span data-stu-id="a0ade-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a0ade-450">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="a0ade-450">Bind an array to a class</span></span>

<span data-ttu-id="a0ade-451">Le fournisseur est capable de lire les valeurs de configuration dans un tableau de liaison à un tableau POCO.</span><span class="sxs-lookup"><span data-stu-id="a0ade-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="a0ade-452">Lors de la lecture à partir d’une source de configuration qui permet aux touches de contenir des séparateurs du côlon (`:`) une clé`:0:`numérique est utilisée pour distinguer les touches qui composent un tableau ( , `:1:`. &hellip; `:{n}:`. .</span><span class="sxs-lookup"><span data-stu-id="a0ade-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="a0ade-453">Pour plus d’informations, voir [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="a0ade-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="a0ade-454">Les touches Azure Key Vault ne peuvent pas utiliser un côlon comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="a0ade-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="a0ade-455">L’approche décrite dans ce sujet`--`utilise double tirets ( ) comme séparateur de valeurs hiérarchiques (sections).</span><span class="sxs-lookup"><span data-stu-id="a0ade-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="a0ade-456">Les touches de tableau sont stockées dans Azure`--0--`Key `--1--` &hellip; `--{n}--`Vault avec des tirets doubles et des segments de clés numériques ( , . . .</span><span class="sxs-lookup"><span data-stu-id="a0ade-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="a0ade-457">Examinez la configuration suivante du fournisseur d’enregistrement [Serilog](https://serilog.net/) fournie par un fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="a0ade-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="a0ade-458">Il y a deux littérals d’objet définis dans le `WriteTo` tableau qui reflètent deux *éviers*de Serilog, qui décrivent des destinations pour la sortie d’exploitation forestière :</span><span class="sxs-lookup"><span data-stu-id="a0ade-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="a0ade-459">La configuration indiquée dans le fichier précédent JSON est`--`stockée dans Azure Key Vault à l’aide de doubles segments de tableau de bord () et numériques :</span><span class="sxs-lookup"><span data-stu-id="a0ade-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="a0ade-460">Clé</span><span class="sxs-lookup"><span data-stu-id="a0ade-460">Key</span></span> | <span data-ttu-id="a0ade-461">Valeur</span><span class="sxs-lookup"><span data-stu-id="a0ade-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="a0ade-462">Recharger les secrets</span><span class="sxs-lookup"><span data-stu-id="a0ade-462">Reload secrets</span></span>

<span data-ttu-id="a0ade-463">Les secrets sont `IConfigurationRoot.Reload()` mis en cache jusqu’à ce qu’on l’appelle.</span><span class="sxs-lookup"><span data-stu-id="a0ade-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="a0ade-464">Les secrets périmés, désactivés et mis à `Reload` jour dans le coffre-fort n’ont pas été respectés par l’application tant qu’ils ne sont pas exécutés.</span><span class="sxs-lookup"><span data-stu-id="a0ade-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="a0ade-465">Secrets handicapés et expirés</span><span class="sxs-lookup"><span data-stu-id="a0ade-465">Disabled and expired secrets</span></span>

<span data-ttu-id="a0ade-466">Les secrets handicapés <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>et expirés jettent un .</span><span class="sxs-lookup"><span data-stu-id="a0ade-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="a0ade-467">Pour empêcher l’application de lancer, fournissez la configuration à l’aide d’un fournisseur de configuration différent ou mettez à jour le secret désactivé ou expiré.</span><span class="sxs-lookup"><span data-stu-id="a0ade-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a0ade-468">Dépanner</span><span class="sxs-lookup"><span data-stu-id="a0ade-468">Troubleshoot</span></span>

<span data-ttu-id="a0ade-469">Lorsque l’application ne charge pas la configuration à l’aide du fournisseur, un message d’erreur est écrit à [l’infrastructure ASP.NET Core Logging](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a0ade-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a0ade-470">Les conditions suivantes empêcheront la configuration de charger :</span><span class="sxs-lookup"><span data-stu-id="a0ade-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="a0ade-471">L’application ou le certificat n’est pas configuré correctement dans Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a0ade-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="a0ade-472">La voûte clé n’existe pas dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0ade-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="a0ade-473">L’application n’est pas autorisée à accéder à la voûte clé.</span><span class="sxs-lookup"><span data-stu-id="a0ade-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="a0ade-474">La politique d’accès `Get` `List` n’inclut pas et les autorisations.</span><span class="sxs-lookup"><span data-stu-id="a0ade-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="a0ade-475">Dans le coffre-fort, les données de configuration (paire de nom) sont incorrectement nommées, manquantes, désactivées ou expirées.</span><span class="sxs-lookup"><span data-stu-id="a0ade-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="a0ade-476">L’application a le mauvais`KeyVaultName`nom de coffre-fort`AzureADApplicationId`de clé ( ), Azure AD Application Id ( ), ou Azure AD certificat thumbprint (`AzureADCertThumbprint`).</span><span class="sxs-lookup"><span data-stu-id="a0ade-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="a0ade-477">La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.</span><span class="sxs-lookup"><span data-stu-id="a0ade-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="a0ade-478">Lors de l’ajout de la stratégie d’accès de l’application à la chambre forte, la stratégie a été créée, mais le bouton **Enregistrer** n’a pas été sélectionné dans l’interface utilisateur **des stratégies d’accès.**</span><span class="sxs-lookup"><span data-stu-id="a0ade-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0ade-479">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a0ade-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="a0ade-480">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0ade-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="a0ade-481">Microsoft Azure: Key Vault Documentation</span><span class="sxs-lookup"><span data-stu-id="a0ade-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="a0ade-482">Comment générer et transférer des clés protégées par HSM pour Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0ade-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="a0ade-483">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="a0ade-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="a0ade-484">Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET</span><span class="sxs-lookup"><span data-stu-id="a0ade-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="a0ade-485">Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Windows Azure et le langage .NET</span><span class="sxs-lookup"><span data-stu-id="a0ade-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

