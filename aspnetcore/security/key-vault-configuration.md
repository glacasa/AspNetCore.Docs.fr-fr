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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Fournisseur de configuration de coffre-fort Azure dans ASP.NET Core

Par [Andrew Stanton-Nurse](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

Ce document explique comment utiliser le fournisseur [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Pour charger les valeurs de configuration des applications à partir des secrets Azure Key Vault. Azure Key Vault est un service basé sur le cloud qui aide à protéger les clés cryptographiques et les secrets utilisés par les applications et les services. Les scénarios courants pour l’utilisation d’Azure Key Vault avec des applications ASP.NET Core incluent :

* Contrôle de l’accès aux données de configuration sensibles.
* Répondre à l’exigence de FIPS 140-2 Niveau 2 validé modules de sécurité matérielle (HSM) lors du stockage des données de configuration.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="packages"></a>.

Ajoutez une référence de paquet au package [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)

## <a name="sample-app"></a>Exemple d'application

L’application d’échantillon s’exécute `#define` dans l’un des deux modes déterminés par la déclaration en haut du fichier *Program.cs* :

* `Certificate`&ndash; Démontre l’utilisation d’un id De client Azure Key Vault et d’un certificat X.509 pour accéder aux secrets stockés dans Azure Key Vault. Cette version de l’échantillon peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou tout hôte capable de servir une application ASP.NET Core.
* `Managed`&ndash; Démontre comment utiliser [les identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l’application à Azure Key Vault avec l’authentification Azure AD sans informations d’identification stockées dans le code ou la configuration de l’application. Lors de l’utilisation d’identités gérées pour authentifier, un ID d’application Azure AD et mot de passe (Secret client) ne sont pas nécessaires. La `Managed` version de l’échantillon doit être déployée à Azure. Suivez les conseils dans la section [Utiliser les identités gérées pour les ressources Azure.](#use-managed-identities-for-azure-resources)

Pour plus d’informations sur la façon de`#define`configurer <xref:index#preprocessor-directives-in-sample-code>une application d’échantillon à l’aide de directives de préprocesseur ( ), voir .

## <a name="secret-storage-in-the-development-environment"></a>Stockage secret dans l’environnement de développement

Définissez des secrets localement à l’aide de [l’outil Secret Manager](xref:security/app-secrets). Lorsque l’application d’échantillon s’exécute sur la machine locale dans l’environnement de développement, les secrets sont chargés à partir du magasin secret manager local.

L’outil Secret `<UserSecretsId>` Manager nécessite une propriété dans le fichier de projet de l’application. Définissez la`{GUID}`valeur de la propriété ( ) à n’importe quel GUID unique :

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Les secrets sont créés comme paires de valeurs nom. Les valeurs hiérarchiques (sections de configuration) utilisent un `:` (colon) comme séparateur dans ASP.NET noms clés de configuration de [base.](xref:fundamentals/configuration/index)

Le Secret Manager est utilisé à partir d’une coque `{SECRET NAME}` de commande `{SECRET VALUE}` ouverte à la racine de [contenu](xref:fundamentals/index#content-root)du projet , où est le nom et est la valeur:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Exécutez les commandes suivantes dans une coque de commande à partir de la racine de [contenu](xref:fundamentals/index#content-root) du projet pour définir les secrets de l’application d’échantillon :

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Lorsque ces secrets sont stockés dans Azure Key Vault dans le [stockage Secret dans l’environnement de production avec la section Azure Key Vault,](#secret-storage-in-the-production-environment-with-azure-key-vault) le `_dev` suffixe est changé en `_prod`. Le suffixe fournit un indice visuel dans la sortie de l’application indiquant la source des valeurs de configuration.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Stockage secret dans l’environnement de production avec Azure Key Vault

Les instructions fournies par le [Quickstart: Set et récupérer un secret à partir d’Azure Key Vault à l’aide du sujet Azure CLI](/azure/key-vault/quick-create-cli) sont résumées ici pour la création d’un coffre-fort Azure et le stockage des secrets utilisés par l’application échantillon. Consultez le sujet pour plus de détails.

1. Ouvrez la coque Azure Cloud en utilisant l’une des méthodes suivantes dans le [portail Azure](https://portal.azure.com/):

   * Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code. Utilisez la chaîne de recherche "Azure CLI" dans la boîte à texte.
   * Ouvrez Cloud Shell dans votre navigateur avec le bouton **Launch Cloud Shell.**
   * Sélectionnez le bouton **Cloud Shell** sur le menu dans le coin supérieur droit du portail Azure.

   Pour plus d’informations, voir [Azure CLI](/cli/azure/) et [Aperçu d’Azure Cloud Shell](/azure/cloud-shell/overview).

1. Si vous n’êtes pas déjà authentifié, connectez-vous avec la `az login` commande.

1. Créer un groupe de ressources `{RESOURCE GROUP NAME}` avec la commande suivante, où `{LOCATION}` est le nom du groupe de ressources pour le nouveau groupe de ressources et est la région Azure (datacenter):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Créer une voûte clé dans le groupe `{KEY VAULT NAME}` de ressources avec la commande `{LOCATION}` suivante, où est le nom pour la nouvelle voûte clé et est la région Azure (datacenter):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Créez des secrets dans la chambre forte comme paires de nom-valeur.

   Les noms secrets Azure Key Vault sont limités aux caractères alphanumériques et aux tirets. Les valeurs hiérarchiques (sections de configuration) utilisent `--` (deux tirets) comme séparateur. Les colons, qui sont normalement utilisés pour délimiter une section à partir d’un sous-clé dans [ASP.NET configuration Core](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms secrets de voûte clé. Par conséquent, deux tirets sont utilisés et échangés contre un côlon lorsque les secrets sont chargés dans la configuration de l’application.

   Les secrets suivants sont à utiliser avec l’application échantillon. Les valeurs `_prod` comprennent un suffixe `_dev` pour les distinguer des valeurs de suffixe chargées dans l’environnement de développement des secrets d’utilisateur. Remplacez-le `{KEY VAULT NAME}` par le nom de la voûte clé que vous avez créée dans l’étape précédente :

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Utilisez l’ID d’application et le certificat X.509 pour les applications non hébergées par Azure

Configurez Azure AD, Azure Key Vault et l’application pour utiliser un identifiant d’application d’annuaire actif Azure et un certificat X.509 pour vous authentifier à un coffre-fort **clé lorsque l’application est hébergée en dehors d’Azure.** Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Bien que l’utilisation d’un ID d’application et d’un certificat X.509 soit pris en charge pour les applications hébergées à Azure, nous vous recommandons d’utiliser [les identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) lors de l’hébergement d’une application à Azure. Les identités gérées ne nécessitent pas de stockage d’un certificat dans l’application ou dans l’environnement de développement.

L’application d’échantillon utilise un ID d’application `#define` et un certificat X.509 `Certificate`lorsque la déclaration en haut du fichier *Program.cs* est définie .

1. Créez un certificat d’archive PKCS-12 *(.pfx).* Les options pour créer des certificats incluent [MakeCert sur Windows](/windows/desktop/seccrypto/makecert) et [OpenSSL](https://www.openssl.org/).
1. Installez le certificat dans le magasin de certificats personnels de l’utilisateur actuel. Le marquage de la clé comme exportable est facultatif. Notez l’empreinte du pouce du certificat, qui est utilisé plus tard dans ce processus.
1. Exporter le certificat d’archive D’archives PKCS 12 (*.pfx*) sous forme de certificat codé par DER (*.cer*).
1. Enregistrez l’application avec Azure AD **(enregistrements d’applications**).
1. Téléchargez le certificat codé DER (*.cer*) sur Azure AD :
   1. Sélectionnez l’application dans Azure AD.
   1. Naviguez vers **les certificats & secrets**.
   1. Sélectionnez **le certificat De téléchargement** pour télécharger le certificat, qui contient la clé publique. Un *certificat .cer*, *.pem*, ou *.crt* est acceptable.
1. Stockez le nom de coffre-fort, l’id d’application et l’empreinte de pouce de certificat dans le fichier *appsettings.json* de l’application.
1. Naviguez vers **les voûtes Key** dans le portail Azure.
1. Sélectionnez la voûte clé que vous avez créée dans le [stockage Secret dans l’environnement Production avec la section Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)
1. Sélectionnez les **politiques d’accès**.
1. Sélectionnez **Ajouter la politique d’accès**.
1. Ouvrez **les autorisations secrètes** et fournissez à l’application les autorisations **Get** and **List.**
1. Sélectionnez **Select principal** et sélectionnez l’application enregistrée par nom. Sélectionnez le bouton **Sélectionner**.
1. Sélectionnez **OK**.
1. Sélectionnez **Enregistrer**.
1. Déployez l’application.

L’application `Certificate` d’échantillon obtient `IConfigurationRoot` ses valeurs de configuration du même nom que le nom secret :

* Valeurs non hiérarchiques : `SecretName` La valeur `config["SecretName"]`pour est obtenue avec .
* Valeurs hiérarchiques (sections) : `:` Utilisation `GetSection` (colon) notation ou méthode d’extension. Utilisez l’une ou l’autre de ces approches pour obtenir la valeur de configuration :
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Le certificat X.509 est géré par le système d’exploitation. L’application <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> appelle avec des valeurs fournies par le fichier *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Exemples de valeurs :

* Nom de voûte clé:`contosovault`
* ID d’application :`627e911e-43cc-61d4-992e-12db9c81b413`
* Empreinte de pouce du certificat :`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Lorsque vous exécutez l’application, une page Web affiche les valeurs secrètes chargées. Dans l’environnement de développement, `_dev` les valeurs secrètes se chargent avec le suffixe. Dans l’environnement de production, `_prod` les valeurs se chargent avec le suffixe.

## <a name="use-managed-identities-for-azure-resources"></a>Utiliser des identités gérées pour les ressources Azure

**Une application déployée sur Azure** peut tirer parti des [identités gérées pour les ressources Azure,](/azure/active-directory/managed-identities-azure-resources/overview)ce qui permet à l’application de s’authentifier avec Azure Key Vault à l’aide d’authentification Azure AD sans informations d’identification (Id d’application et Mot de Passe/Client Secret) stockée dans l’application.

L’application d’échantillon utilise des `#define` identités gérées pour les ressources Azure `Managed`lorsque la déclaration en haut du fichier *Program.cs* est définie à .

Entrez le nom de coffre-fort dans le fichier *appsettings.json* de l’application. L’application d’échantillon ne nécessite pas d’identifiant d’application et de mot de passe (Secret client) lorsqu’elle est configurée sur la `Managed` version, de sorte que vous pouvez ignorer ces entrées de configuration. L’application est déployée sur Azure, et Azure authentifie l’application pour accéder à Azure Key Vault uniquement en utilisant le nom de voûte stocké dans le fichier *appsettings.json.*

Déployez l’application d’échantillon au service d’application Azure.

Une application déployée sur Azure App Service est automatiquement enregistrée auprès d’Azure AD lors de la création du service. Obtenez l’identifiant d’objet du déploiement pour une utilisation dans la commande suivante. L’identifiant d’objet est affiché dans le portail Azure sur le panneau **d’identité** du service d’application.

En utilisant Azure CLI et l’identifiant d’objet de l’application, fournissez à l’application `list` et `get` autorisez-vous à accéder à la voûte clé :

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Redémarrez l’application** à l’aide d’Azure CLI, PowerShell ou du portail Azure.

L’application échantillon:

* Crée une instance `AzureServiceTokenProvider` de la classe sans chaîne de connexion. Lorsqu’une chaîne de connexion n’est pas fournie, le fournisseur tente d’obtenir un jeton d’identités gérées pour les ressources Azure.
* Un <xref:Microsoft.Azure.KeyVault.KeyVaultClient> nouveau est `AzureServiceTokenProvider` créé avec l’exemple rappel symbolique.
* L’instance <xref:Microsoft.Azure.KeyVault.KeyVaultClient> est utilisée avec <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> une implémentation par défaut qui`--`charge toutes les`:`valeurs secrètes et remplace les doubles-dashes () par des colons ( ) dans les noms clés.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Valeur de l’exemple du nom de coffre-fort :`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Lorsque vous exécutez l’application, une page Web affiche les valeurs secrètes chargées. Dans l’environnement de développement, les valeurs secrètes ont le `_dev` suffixe parce qu’elles sont fournies par les secrets d’utilisateur. Dans l’environnement de production, `_prod` les valeurs se chargent avec le suffixe car ils sont fournis par Azure Key Vault.

Si vous `Access denied` recevez une erreur, confirmez que l’application est enregistrée auprès d’Azure AD et avez donné accès au coffre-fort. Confirmez que vous avez redémarré le service à Azure.

Pour plus d’informations sur l’utilisation du fournisseur avec une identité gérée et un pipeline Azure DevOps, voir [Créer une connexion de service Azure Resource Manager à une VM avec une identité de service gérée](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="configuration-options"></a>Options de configuration

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>peut accepter <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>un :

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Propriété         | Description |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>à utiliser pour récupérer les valeurs. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>instance utilisée pour contrôler le chargement secret. |
| `ReloadInterval` | `Timespan`d’attendre entre les tentatives de sondage de la voûte clé pour les changements. La valeur `null` par défaut est (la configuration n’est pas rechargée). |
| `Vault`          | Clé voûte URI. |

## <a name="use-a-key-name-prefix"></a>Utilisez un préfixe de nom clé

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fournit une surcharge qui accepte <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>une implémentation de , qui vous permet de contrôler la façon dont les secrets de voûte clés sont convertis en clés de configuration. Par exemple, vous pouvez implémenter l’interface pour charger des valeurs secrètes en fonction d’une valeur préfixe que vous fournissez lors du démarrage de l’application. Cela vous permet, par exemple, de charger des secrets basés sur la version de l’application.

> [!WARNING]
> N’utilisez pas de préfixes sur les secrets clés de voûte pour placer des secrets pour plusieurs applications dans la même voûte clé ou pour placer des secrets environnementaux (par exemple, *le développement* contre les secrets *de production)* dans la même voûte. Nous recommandons que différentes applications et environnements de développement/production utilisent des coffres-forts distincts pour isoler les environnements d’applications pour le plus haut niveau de sécurité.

Dans l’exemple suivant, un secret est établi dans la chambre forte (et en utilisant l’outil Secret Manager pour l’environnement de développement) pour `5000-AppSecret` (les périodes ne sont pas autorisées dans les noms secrets de voûte clé). Ce secret représente un secret d’application pour la version 5.0.0.0 de l’application. Pour une autre version de l’application, 5.1.0.0, un secret est ajouté `5100-AppSecret`à la voûte clé (et en utilisant l’outil Secret Manager) pour . Chaque version d’application charge sa valeur `AppSecret`secrète versionnée dans sa configuration comme, dépouillant la version pendant qu’elle charge le secret.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>est appelé avec <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>une coutume :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

La <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> mise en œuvre réagit aux préfixes de la version des secrets pour charger le secret approprié dans la configuration:

* `Load`charge un secret lorsque son nom commence par le préfixe. D’autres secrets ne sont pas chargés.
* `GetKey`:
  * Supprime le préfixe du nom secret.
  * Remplace deux tirets dans n’importe quel nom avec le `KeyDelimiter`, qui est le délimital utilisé en configuration (généralement un côlon). Azure Key Vault ne permet pas un côlon dans des noms secrets.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

La `Load` méthode est appelée par un algorithme de fournisseur qui itère à travers les secrets de voûte pour trouver ceux qui ont le préfixe de version. Lorsqu’un préfixe `Load`de version est `GetKey` trouvé avec, l’algorithme utilise la méthode pour retourner le nom de configuration du nom secret. Il dépouille le préfixe de la version du nom du secret et renvoie le reste du nom secret pour le chargement dans les paires de nom de configuration de l’application.

Lorsque cette approche est mise en œuvre :

1. La version de l’application spécifiée dans le fichier de projet de l’application. Dans l’exemple suivant, la version `5.0.0.0`de l’application est définie pour :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Confirmez `<UserSecretsId>` qu’une propriété est présente dans `{GUID}` le fichier de projet de l’application, où se trouve un GUID fourni par l’utilisateur :

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Enregistrez les secrets suivants localement avec [l’outil Secret Manager](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Les secrets sont enregistrés dans Azure Key Vault à l’aide des commandes Azure CLI suivantes :

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Lorsque l’application est exécutée, les secrets de voûte clés sont chargés. Le secret `5000-AppSecret` de la chaîne est adapté à la version de`5.0.0.0`l’application spécifiée dans le fichier de projet de l’application ( ).

1. La version, `5000` (avec le tableau de bord), est dépouillée du nom clé. Tout au long de l’application, la configuration de lecture avec la clé `AppSecret` charge la valeur secrète.

1. Si la version de l’application est `5.1.0.0` modifiée dans le fichier du projet `5.1.0.0_secret_value_dev` et que `5.1.0.0_secret_value_prod` l’application est exécutée à nouveau, la valeur secrète retournée est dans l’environnement de développement et dans la production.

> [!NOTE]
> Vous pouvez également <xref:Microsoft.Azure.KeyVault.KeyVaultClient> fournir <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>votre propre implémentation à . Un client personnalisé permet de partager une seule instance du client à travers l’application.

## <a name="bind-an-array-to-a-class"></a>Lier un tableau à une classe

Le fournisseur est capable de lire les valeurs de configuration dans un tableau de liaison à un tableau POCO.

Lors de la lecture à partir d’une source de configuration qui permet aux touches de contenir des séparateurs du côlon (`:`) une clé`:0:`numérique est utilisée pour distinguer les touches qui composent un tableau ( , `:1:`. &hellip; `:{n}:`. . Pour plus d’informations, voir [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Les touches Azure Key Vault ne peuvent pas utiliser un côlon comme séparateur. L’approche décrite dans ce sujet`--`utilise double tirets ( ) comme séparateur de valeurs hiérarchiques (sections). Les touches de tableau sont stockées dans Azure`--0--`Key `--1--` &hellip; `--{n}--`Vault avec des tirets doubles et des segments de clés numériques ( , . . .

Examinez la configuration suivante du fournisseur d’enregistrement [Serilog](https://serilog.net/) fournie par un fichier JSON. Il y a deux littérals d’objet définis dans le `WriteTo` tableau qui reflètent deux *éviers*de Serilog, qui décrivent des destinations pour la sortie d’exploitation forestière :

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

La configuration indiquée dans le fichier précédent JSON est`--`stockée dans Azure Key Vault à l’aide de doubles segments de tableau de bord () et numériques :

| Clé | Valeur |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Recharger les secrets

Les secrets sont `IConfigurationRoot.Reload()` mis en cache jusqu’à ce qu’on l’appelle. Les secrets périmés, désactivés et mis à `Reload` jour dans le coffre-fort n’ont pas été respectés par l’application tant qu’ils ne sont pas exécutés.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Secrets handicapés et expirés

Les secrets handicapés <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>et expirés jettent un . Pour empêcher l’application de lancer, fournissez la configuration à l’aide d’un fournisseur de configuration différent ou mettez à jour le secret désactivé ou expiré.

## <a name="troubleshoot"></a>Dépanner

Lorsque l’application ne charge pas la configuration à l’aide du fournisseur, un message d’erreur est écrit à [l’infrastructure ASP.NET Core Logging](xref:fundamentals/logging/index). Les conditions suivantes empêcheront la configuration de charger :

* L’application ou le certificat n’est pas configuré correctement dans Azure Active Directory.
* La voûte clé n’existe pas dans Azure Key Vault.
* L’application n’est pas autorisée à accéder à la voûte clé.
* La politique d’accès `Get` `List` n’inclut pas et les autorisations.
* Dans le coffre-fort, les données de configuration (paire de nom) sont incorrectement nommées, manquantes, désactivées ou expirées.
* L’application a le mauvais`KeyVaultName`nom de coffre-fort`AzureADApplicationId`de clé ( ), Azure AD Application Id ( ), ou Azure AD certificat thumbprint (`AzureADCertThumbprint`).
* La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.
* Lors de l’ajout de la stratégie d’accès de l’application à la chambre forte, la stratégie a été créée, mais le bouton **Enregistrer** n’a pas été sélectionné dans l’interface utilisateur **des stratégies d’accès.**

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault Documentation](/azure/key-vault/)
* [Comment générer et transférer des clés protégées par HSM pour Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET](/azure/key-vault/quick-create-net)
* [Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Windows Azure et le langage .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ce document explique comment utiliser le fournisseur [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Pour charger les valeurs de configuration des applications à partir des secrets Azure Key Vault. Azure Key Vault est un service basé sur le cloud qui aide à protéger les clés cryptographiques et les secrets utilisés par les applications et les services. Les scénarios courants pour l’utilisation d’Azure Key Vault avec des applications ASP.NET Core incluent :

* Contrôle de l’accès aux données de configuration sensibles.
* Répondre à l’exigence de FIPS 140-2 Niveau 2 validé modules de sécurité matérielle (HSM) lors du stockage des données de configuration.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="packages"></a>.

Ajoutez une référence de paquet au package [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)

## <a name="sample-app"></a>Exemple d'application

L’application d’échantillon s’exécute `#define` dans l’un des deux modes déterminés par la déclaration en haut du fichier *Program.cs* :

* `Certificate`&ndash; Démontre l’utilisation d’un id De client Azure Key Vault et d’un certificat X.509 pour accéder aux secrets stockés dans Azure Key Vault. Cette version de l’échantillon peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou tout hôte capable de servir une application ASP.NET Core.
* `Managed`&ndash; Démontre comment utiliser [les identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l’application à Azure Key Vault avec l’authentification Azure AD sans informations d’identification stockées dans le code ou la configuration de l’application. Lors de l’utilisation d’identités gérées pour authentifier, un ID d’application Azure AD et mot de passe (Secret client) ne sont pas nécessaires. La `Managed` version de l’échantillon doit être déployée à Azure. Suivez les conseils dans la section [Utiliser les identités gérées pour les ressources Azure.](#use-managed-identities-for-azure-resources)

Pour plus d’informations sur la façon de`#define`configurer <xref:index#preprocessor-directives-in-sample-code>une application d’échantillon à l’aide de directives de préprocesseur ( ), voir .

## <a name="secret-storage-in-the-development-environment"></a>Stockage secret dans l’environnement de développement

Définissez des secrets localement à l’aide de [l’outil Secret Manager](xref:security/app-secrets). Lorsque l’application d’échantillon s’exécute sur la machine locale dans l’environnement de développement, les secrets sont chargés à partir du magasin secret manager local.

L’outil Secret `<UserSecretsId>` Manager nécessite une propriété dans le fichier de projet de l’application. Définissez la`{GUID}`valeur de la propriété ( ) à n’importe quel GUID unique :

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Les secrets sont créés comme paires de valeurs nom. Les valeurs hiérarchiques (sections de configuration) utilisent un `:` (colon) comme séparateur dans ASP.NET noms clés de configuration de [base.](xref:fundamentals/configuration/index)

Le Secret Manager est utilisé à partir d’une coque `{SECRET NAME}` de commande `{SECRET VALUE}` ouverte à la racine de [contenu](xref:fundamentals/index#content-root)du projet , où est le nom et est la valeur:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Exécutez les commandes suivantes dans une coque de commande à partir de la racine de [contenu](xref:fundamentals/index#content-root) du projet pour définir les secrets de l’application d’échantillon :

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Lorsque ces secrets sont stockés dans Azure Key Vault dans le [stockage Secret dans l’environnement de production avec la section Azure Key Vault,](#secret-storage-in-the-production-environment-with-azure-key-vault) le `_dev` suffixe est changé en `_prod`. Le suffixe fournit un indice visuel dans la sortie de l’application indiquant la source des valeurs de configuration.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Stockage secret dans l’environnement de production avec Azure Key Vault

Les instructions fournies par le [Quickstart: Set et récupérer un secret à partir d’Azure Key Vault à l’aide du sujet Azure CLI](/azure/key-vault/quick-create-cli) sont résumées ici pour la création d’un coffre-fort Azure et le stockage des secrets utilisés par l’application échantillon. Consultez le sujet pour plus de détails.

1. Ouvrez la coque Azure Cloud en utilisant l’une des méthodes suivantes dans le [portail Azure](https://portal.azure.com/):

   * Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code. Utilisez la chaîne de recherche "Azure CLI" dans la boîte à texte.
   * Ouvrez Cloud Shell dans votre navigateur avec le bouton **Launch Cloud Shell.**
   * Sélectionnez le bouton **Cloud Shell** sur le menu dans le coin supérieur droit du portail Azure.

   Pour plus d’informations, voir [Azure CLI](/cli/azure/) et [Aperçu d’Azure Cloud Shell](/azure/cloud-shell/overview).

1. Si vous n’êtes pas déjà authentifié, connectez-vous avec la `az login` commande.

1. Créer un groupe de ressources `{RESOURCE GROUP NAME}` avec la commande suivante, où `{LOCATION}` est le nom du groupe de ressources pour le nouveau groupe de ressources et est la région Azure (datacenter):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Créer une voûte clé dans le groupe `{KEY VAULT NAME}` de ressources avec la commande `{LOCATION}` suivante, où est le nom pour la nouvelle voûte clé et est la région Azure (datacenter):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Créez des secrets dans la chambre forte comme paires de nom-valeur.

   Les noms secrets Azure Key Vault sont limités aux caractères alphanumériques et aux tirets. Les valeurs hiérarchiques (sections de configuration) utilisent `--` (deux tirets) comme séparateur. Les colons, qui sont normalement utilisés pour délimiter une section à partir d’un sous-clé dans [ASP.NET configuration Core](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms secrets de voûte clé. Par conséquent, deux tirets sont utilisés et échangés contre un côlon lorsque les secrets sont chargés dans la configuration de l’application.

   Les secrets suivants sont à utiliser avec l’application échantillon. Les valeurs `_prod` comprennent un suffixe `_dev` pour les distinguer des valeurs de suffixe chargées dans l’environnement de développement des secrets d’utilisateur. Remplacez-le `{KEY VAULT NAME}` par le nom de la voûte clé que vous avez créée dans l’étape précédente :

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Utilisez l’ID d’application et le certificat X.509 pour les applications non hébergées par Azure

Configurez Azure AD, Azure Key Vault et l’application pour utiliser un identifiant d’application d’annuaire actif Azure et un certificat X.509 pour vous authentifier à un coffre-fort **clé lorsque l’application est hébergée en dehors d’Azure.** Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Bien que l’utilisation d’un ID d’application et d’un certificat X.509 soit pris en charge pour les applications hébergées à Azure, nous vous recommandons d’utiliser [les identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) lors de l’hébergement d’une application à Azure. Les identités gérées ne nécessitent pas de stockage d’un certificat dans l’application ou dans l’environnement de développement.

L’application d’échantillon utilise un ID d’application `#define` et un certificat X.509 `Certificate`lorsque la déclaration en haut du fichier *Program.cs* est définie .

1. Créez un certificat d’archive PKCS-12 *(.pfx).* Les options pour créer des certificats incluent [MakeCert sur Windows](/windows/desktop/seccrypto/makecert) et [OpenSSL](https://www.openssl.org/).
1. Installez le certificat dans le magasin de certificats personnels de l’utilisateur actuel. Le marquage de la clé comme exportable est facultatif. Notez l’empreinte du pouce du certificat, qui est utilisé plus tard dans ce processus.
1. Exporter le certificat d’archive D’archives PKCS 12 (*.pfx*) sous forme de certificat codé par DER (*.cer*).
1. Enregistrez l’application avec Azure AD **(enregistrements d’applications**).
1. Téléchargez le certificat codé DER (*.cer*) sur Azure AD :
   1. Sélectionnez l’application dans Azure AD.
   1. Naviguez vers **les certificats & secrets**.
   1. Sélectionnez **le certificat De téléchargement** pour télécharger le certificat, qui contient la clé publique. Un *certificat .cer*, *.pem*, ou *.crt* est acceptable.
1. Stockez le nom de coffre-fort, l’id d’application et l’empreinte de pouce de certificat dans le fichier *appsettings.json* de l’application.
1. Naviguez vers **les voûtes Key** dans le portail Azure.
1. Sélectionnez la voûte clé que vous avez créée dans le [stockage Secret dans l’environnement Production avec la section Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)
1. Sélectionnez les **politiques d’accès**.
1. Sélectionnez **Ajouter la politique d’accès**.
1. Ouvrez **les autorisations secrètes** et fournissez à l’application les autorisations **Get** and **List.**
1. Sélectionnez **Select principal** et sélectionnez l’application enregistrée par nom. Sélectionnez le bouton **Sélectionner**.
1. Sélectionnez **OK**.
1. Sélectionnez **Enregistrer**.
1. Déployez l’application.

L’application `Certificate` d’échantillon obtient `IConfigurationRoot` ses valeurs de configuration du même nom que le nom secret :

* Valeurs non hiérarchiques : `SecretName` La valeur `config["SecretName"]`pour est obtenue avec .
* Valeurs hiérarchiques (sections) : `:` Utilisation `GetSection` (colon) notation ou méthode d’extension. Utilisez l’une ou l’autre de ces approches pour obtenir la valeur de configuration :
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Le certificat X.509 est géré par le système d’exploitation. L’application <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> appelle avec des valeurs fournies par le fichier *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Exemples de valeurs :

* Nom de voûte clé:`contosovault`
* ID d’application :`627e911e-43cc-61d4-992e-12db9c81b413`
* Empreinte de pouce du certificat :`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Lorsque vous exécutez l’application, une page Web affiche les valeurs secrètes chargées. Dans l’environnement de développement, `_dev` les valeurs secrètes se chargent avec le suffixe. Dans l’environnement de production, `_prod` les valeurs se chargent avec le suffixe.

## <a name="use-managed-identities-for-azure-resources"></a>Utiliser des identités gérées pour les ressources Azure

**Une application déployée sur Azure** peut tirer parti des [identités gérées pour les ressources Azure,](/azure/active-directory/managed-identities-azure-resources/overview)ce qui permet à l’application de s’authentifier avec Azure Key Vault à l’aide d’authentification Azure AD sans informations d’identification (Id d’application et Mot de Passe/Client Secret) stockée dans l’application.

L’application d’échantillon utilise des `#define` identités gérées pour les ressources Azure `Managed`lorsque la déclaration en haut du fichier *Program.cs* est définie à .

Entrez le nom de coffre-fort dans le fichier *appsettings.json* de l’application. L’application d’échantillon ne nécessite pas d’identifiant d’application et de mot de passe (Secret client) lorsqu’elle est configurée sur la `Managed` version, de sorte que vous pouvez ignorer ces entrées de configuration. L’application est déployée sur Azure, et Azure authentifie l’application pour accéder à Azure Key Vault uniquement en utilisant le nom de voûte stocké dans le fichier *appsettings.json.*

Déployez l’application d’échantillon au service d’application Azure.

Une application déployée sur Azure App Service est automatiquement enregistrée auprès d’Azure AD lors de la création du service. Obtenez l’identifiant d’objet du déploiement pour une utilisation dans la commande suivante. L’identifiant d’objet est affiché dans le portail Azure sur le panneau **d’identité** du service d’application.

En utilisant Azure CLI et l’identifiant d’objet de l’application, fournissez à l’application `list` et `get` autorisez-vous à accéder à la voûte clé :

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Redémarrez l’application** à l’aide d’Azure CLI, PowerShell ou du portail Azure.

L’application échantillon:

* Crée une instance `AzureServiceTokenProvider` de la classe sans chaîne de connexion. Lorsqu’une chaîne de connexion n’est pas fournie, le fournisseur tente d’obtenir un jeton d’identités gérées pour les ressources Azure.
* Un <xref:Microsoft.Azure.KeyVault.KeyVaultClient> nouveau est `AzureServiceTokenProvider` créé avec l’exemple rappel symbolique.
* L’instance <xref:Microsoft.Azure.KeyVault.KeyVaultClient> est utilisée avec <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> une implémentation par défaut qui`--`charge toutes les`:`valeurs secrètes et remplace les doubles-dashes () par des colons ( ) dans les noms clés.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Valeur de l’exemple du nom de coffre-fort :`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Lorsque vous exécutez l’application, une page Web affiche les valeurs secrètes chargées. Dans l’environnement de développement, les valeurs secrètes ont le `_dev` suffixe parce qu’elles sont fournies par les secrets d’utilisateur. Dans l’environnement de production, `_prod` les valeurs se chargent avec le suffixe car ils sont fournis par Azure Key Vault.

Si vous `Access denied` recevez une erreur, confirmez que l’application est enregistrée auprès d’Azure AD et avez donné accès au coffre-fort. Confirmez que vous avez redémarré le service à Azure.

Pour plus d’informations sur l’utilisation du fournisseur avec une identité gérée et un pipeline Azure DevOps, voir [Créer une connexion de service Azure Resource Manager à une VM avec une identité de service gérée](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="use-a-key-name-prefix"></a>Utilisez un préfixe de nom clé

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fournit une surcharge qui accepte <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>une implémentation de , qui vous permet de contrôler la façon dont les secrets de voûte clés sont convertis en clés de configuration. Par exemple, vous pouvez implémenter l’interface pour charger des valeurs secrètes en fonction d’une valeur préfixe que vous fournissez lors du démarrage de l’application. Cela vous permet, par exemple, de charger des secrets basés sur la version de l’application.

> [!WARNING]
> N’utilisez pas de préfixes sur les secrets clés de voûte pour placer des secrets pour plusieurs applications dans la même voûte clé ou pour placer des secrets environnementaux (par exemple, *le développement* contre les secrets *de production)* dans la même voûte. Nous recommandons que différentes applications et environnements de développement/production utilisent des coffres-forts distincts pour isoler les environnements d’applications pour le plus haut niveau de sécurité.

Dans l’exemple suivant, un secret est établi dans la chambre forte (et en utilisant l’outil Secret Manager pour l’environnement de développement) pour `5000-AppSecret` (les périodes ne sont pas autorisées dans les noms secrets de voûte clé). Ce secret représente un secret d’application pour la version 5.0.0.0 de l’application. Pour une autre version de l’application, 5.1.0.0, un secret est ajouté `5100-AppSecret`à la voûte clé (et en utilisant l’outil Secret Manager) pour . Chaque version d’application charge sa valeur `AppSecret`secrète versionnée dans sa configuration comme, dépouillant la version pendant qu’elle charge le secret.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>est appelé avec <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>une coutume :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

La <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> mise en œuvre réagit aux préfixes de la version des secrets pour charger le secret approprié dans la configuration:

* `Load`charge un secret lorsque son nom commence par le préfixe. D’autres secrets ne sont pas chargés.
* `GetKey`:
  * Supprime le préfixe du nom secret.
  * Remplace deux tirets dans n’importe quel nom avec le `KeyDelimiter`, qui est le délimital utilisé en configuration (généralement un côlon). Azure Key Vault ne permet pas un côlon dans des noms secrets.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

La `Load` méthode est appelée par un algorithme de fournisseur qui itère à travers les secrets de voûte pour trouver ceux qui ont le préfixe de version. Lorsqu’un préfixe `Load`de version est `GetKey` trouvé avec, l’algorithme utilise la méthode pour retourner le nom de configuration du nom secret. Il dépouille le préfixe de la version du nom du secret et renvoie le reste du nom secret pour le chargement dans les paires de nom de configuration de l’application.

Lorsque cette approche est mise en œuvre :

1. La version de l’application spécifiée dans le fichier de projet de l’application. Dans l’exemple suivant, la version `5.0.0.0`de l’application est définie pour :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Confirmez `<UserSecretsId>` qu’une propriété est présente dans `{GUID}` le fichier de projet de l’application, où se trouve un GUID fourni par l’utilisateur :

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Enregistrez les secrets suivants localement avec [l’outil Secret Manager](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Les secrets sont enregistrés dans Azure Key Vault à l’aide des commandes Azure CLI suivantes :

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Lorsque l’application est exécutée, les secrets de voûte clés sont chargés. Le secret `5000-AppSecret` de la chaîne est adapté à la version de`5.0.0.0`l’application spécifiée dans le fichier de projet de l’application ( ).

1. La version, `5000` (avec le tableau de bord), est dépouillée du nom clé. Tout au long de l’application, la configuration de lecture avec la clé `AppSecret` charge la valeur secrète.

1. Si la version de l’application est `5.1.0.0` modifiée dans le fichier du projet `5.1.0.0_secret_value_dev` et que `5.1.0.0_secret_value_prod` l’application est exécutée à nouveau, la valeur secrète retournée est dans l’environnement de développement et dans la production.

> [!NOTE]
> Vous pouvez également <xref:Microsoft.Azure.KeyVault.KeyVaultClient> fournir <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>votre propre implémentation à . Un client personnalisé permet de partager une seule instance du client à travers l’application.

## <a name="bind-an-array-to-a-class"></a>Lier un tableau à une classe

Le fournisseur est capable de lire les valeurs de configuration dans un tableau de liaison à un tableau POCO.

Lors de la lecture à partir d’une source de configuration qui permet aux touches de contenir des séparateurs du côlon (`:`) une clé`:0:`numérique est utilisée pour distinguer les touches qui composent un tableau ( , `:1:`. &hellip; `:{n}:`. . Pour plus d’informations, voir [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Les touches Azure Key Vault ne peuvent pas utiliser un côlon comme séparateur. L’approche décrite dans ce sujet`--`utilise double tirets ( ) comme séparateur de valeurs hiérarchiques (sections). Les touches de tableau sont stockées dans Azure`--0--`Key `--1--` &hellip; `--{n}--`Vault avec des tirets doubles et des segments de clés numériques ( , . . .

Examinez la configuration suivante du fournisseur d’enregistrement [Serilog](https://serilog.net/) fournie par un fichier JSON. Il y a deux littérals d’objet définis dans le `WriteTo` tableau qui reflètent deux *éviers*de Serilog, qui décrivent des destinations pour la sortie d’exploitation forestière :

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

La configuration indiquée dans le fichier précédent JSON est`--`stockée dans Azure Key Vault à l’aide de doubles segments de tableau de bord () et numériques :

| Clé | Valeur |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Recharger les secrets

Les secrets sont `IConfigurationRoot.Reload()` mis en cache jusqu’à ce qu’on l’appelle. Les secrets périmés, désactivés et mis à `Reload` jour dans le coffre-fort n’ont pas été respectés par l’application tant qu’ils ne sont pas exécutés.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Secrets handicapés et expirés

Les secrets handicapés <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>et expirés jettent un . Pour empêcher l’application de lancer, fournissez la configuration à l’aide d’un fournisseur de configuration différent ou mettez à jour le secret désactivé ou expiré.

## <a name="troubleshoot"></a>Dépanner

Lorsque l’application ne charge pas la configuration à l’aide du fournisseur, un message d’erreur est écrit à [l’infrastructure ASP.NET Core Logging](xref:fundamentals/logging/index). Les conditions suivantes empêcheront la configuration de charger :

* L’application ou le certificat n’est pas configuré correctement dans Azure Active Directory.
* La voûte clé n’existe pas dans Azure Key Vault.
* L’application n’est pas autorisée à accéder à la voûte clé.
* La politique d’accès `Get` `List` n’inclut pas et les autorisations.
* Dans le coffre-fort, les données de configuration (paire de nom) sont incorrectement nommées, manquantes, désactivées ou expirées.
* L’application a le mauvais`KeyVaultName`nom de coffre-fort`AzureADApplicationId`de clé ( ), Azure AD Application Id ( ), ou Azure AD certificat thumbprint (`AzureADCertThumbprint`).
* La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.
* Lors de l’ajout de la stratégie d’accès de l’application à la chambre forte, la stratégie a été créée, mais le bouton **Enregistrer** n’a pas été sélectionné dans l’interface utilisateur **des stratégies d’accès.**

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault Documentation](/azure/key-vault/)
* [Comment générer et transférer des clés protégées par HSM pour Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET](/azure/key-vault/quick-create-net)
* [Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Windows Azure et le langage .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

