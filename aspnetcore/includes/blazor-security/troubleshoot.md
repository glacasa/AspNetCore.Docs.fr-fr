## <a name="troubleshoot"></a><span data-ttu-id="b8ae3-101">Dépanner</span><span class="sxs-lookup"><span data-stu-id="b8ae3-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="b8ae3-102">Cookies et données du site</span><span class="sxs-lookup"><span data-stu-id="b8ae3-102">Cookies and site data</span></span>

<span data-ttu-id="b8ae3-103">Les cookies et les données du site peuvent persister dans les mises à jour de l’application et interférer avec les tests et les dépannages.</span><span class="sxs-lookup"><span data-stu-id="b8ae3-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="b8ae3-104">Effacer ce qui suit lors de l’apport de modifications de code d’application, les modifications de compte utilisateur avec le fournisseur, ou les modifications de configuration de l’application fournisseur:</span><span class="sxs-lookup"><span data-stu-id="b8ae3-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="b8ae3-105">Cookies d’inscription de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="b8ae3-105">User sign-in cookies</span></span>
* <span data-ttu-id="b8ae3-106">Cookies App</span><span class="sxs-lookup"><span data-stu-id="b8ae3-106">App cookies</span></span>
* <span data-ttu-id="b8ae3-107">Données du site mises en cache et stockées</span><span class="sxs-lookup"><span data-stu-id="b8ae3-107">Cached and stored site data</span></span>

<span data-ttu-id="b8ae3-108">Une approche pour empêcher les cookies persistants et les données du site d’interférer avec les tests et le dépannage est de :</span><span class="sxs-lookup"><span data-stu-id="b8ae3-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="b8ae3-109">Utilisez un navigateur pour les tests que vous pouvez configurer pour supprimer toutes les données de cookie et de site chaque fois que le navigateur est fermé.</span><span class="sxs-lookup"><span data-stu-id="b8ae3-109">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
* <span data-ttu-id="b8ae3-110">Fermez le navigateur entre toute modification de l’application, de l’utilisateur de test ou de la configuration du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="b8ae3-110">Close the browser between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="b8ae3-111">Exécuter l’application Server</span><span class="sxs-lookup"><span data-stu-id="b8ae3-111">Run the Server app</span></span>

<span data-ttu-id="b8ae3-112">Lors du test et du dépannage d’une application Blazor hébergée, assurez-vous que vous exécutez l’application à partir du projet **Server.**</span><span class="sxs-lookup"><span data-stu-id="b8ae3-112">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="b8ae3-113">Par exemple dans Visual Studio, confirmez que le projet Server est mis en surbrillance dans **Solution Explorer** avant de commencer l’application avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8ae3-113">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="b8ae3-114">Sélectionnez le bouton **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="b8ae3-114">Select the **Run** button.</span></span>
* <span data-ttu-id="b8ae3-115">Utilisez **Debug** > **Start Debugging** à partir du menu.</span><span class="sxs-lookup"><span data-stu-id="b8ae3-115">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="b8ae3-116">Appuyez sur <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="b8ae3-116">Press <kbd>F5</kbd>.</span></span>
