## <a name="troubleshoot"></a>Dépanner

### <a name="cookies-and-site-data"></a>Cookies et données du site

Les cookies et les données du site peuvent persister dans les mises à jour de l’application et interférer avec les tests et les dépannages. Effacer ce qui suit lors de l’apport de modifications de code d’application, les modifications de compte utilisateur avec le fournisseur, ou les modifications de configuration de l’application fournisseur:

* Cookies d’inscription de l’utilisateur
* Cookies App
* Données du site mises en cache et stockées

Une approche pour empêcher les cookies persistants et les données du site d’interférer avec les tests et le dépannage est de :

* Utilisez un navigateur pour les tests que vous pouvez configurer pour supprimer toutes les données de cookie et de site chaque fois que le navigateur est fermé.
* Fermez le navigateur entre toute modification de l’application, de l’utilisateur de test ou de la configuration du fournisseur.

### <a name="run-the-server-app"></a>Exécuter l’application Server

Lors du test et du dépannage d’une application Blazor hébergée, assurez-vous que vous exécutez l’application à partir du projet **Server.** Par exemple dans Visual Studio, confirmez que le projet Server est mis en surbrillance dans **Solution Explorer** avant de commencer l’application avec l’une des approches suivantes :

* Sélectionnez le bouton **Exécuter**.
* Utilisez **Debug** > **Start Debugging** à partir du menu.
* Appuyez sur <kbd>F5</kbd>.
