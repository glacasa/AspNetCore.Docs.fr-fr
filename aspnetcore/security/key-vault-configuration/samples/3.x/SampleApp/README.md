# <a name="key-vault-configuration-provider-sample-app"></a>Exemple d’application de fournisseur de configuration Key Vault

Cet exemple illustre l’utilisation du fournisseur de configuration Azure Key Vault.

L’exemple s’exécute dans l’un des deux modes déterminés par l' `#define` instruction en haut du fichier *Program.cs* . Pour obtenir des instructions, consultez [directives de préprocesseur dans exemple de code](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):

* `Certificate`: Illustre l’utilisation d’un ID client Azure Key Vault et d’un certificat X. 509 pour accéder aux secrets stockés dans Azure Key Vault. Cette version de l’exemple peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou n’importe quel hôte capable de servir une application ASP.NET Core.
* `Managed`: Montre comment utiliser le [Managed Service Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) d’Azure pour authentifier l’application afin de Azure Key Vault avec l’authentification Azure ad sans informations d’identification dans le code ou la configuration de l’application. Un ID client et une clé secrète Azure AD ne sont pas nécessaires pour que l’application s’authentifie avec Azure Key Vault. Cet exemple doit être déployé pour Azure App Service pour explorer l’identité managée scearnio.

Pour plus d’informations, consultez [Azure Key Vault fournisseur de configuration](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration).
