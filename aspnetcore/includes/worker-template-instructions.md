# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Créez un projet.
1. Sélectionnez **Service des travailleurs**. Sélectionnez **Suivant**.
1. Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut. Sélectionnez **Create** (Créer).
1. Dans le **Dialogue Créer un nouveau service de travailleur,** sélectionnez **Créer**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Créez un projet.
1. Sélectionnez **App** sous **.NET Core** dans la barre latérale.
1. Sélectionnez **Travailleur** sous **ASP.NET Core**. Sélectionnez **Suivant**.
1. Sélectionnez **.NET Core 3.0** ou plus tard pour le **cadre cible**. Sélectionnez **Suivant**.
1. Fournir un nom dans le domaine **du nom du projet.** Sélectionnez **Create** (Créer).

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Utilisez le modèle Service Worker (`worker`) avec la commande [dotnet new](/dotnet/core/tools/dotnet-new) à partir d’un interpréteur de commandes. Dans l’exemple suivant, une application Service Worker est créée et se nomme `ContosoWorker`. Un dossier pour l’application `ContosoWorker` est créé automatiquement durant l’exécution de la commande.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
