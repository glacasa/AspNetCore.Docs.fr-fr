<a name="codegenerator"></a> Le tableau suivant détaille les paramètres du générateur de code ASP.NET Core :

| Paramètre               | Description|
| ----------------- | ------------ |
| -M  | Nom du modèle. |
| -dc  | Classe `DbContext` à utiliser. |
| -udl | Utiliser la disposition par défaut. |
| -outDir | Chemin du dossier de sortie relatif pour créer les vues. |
| --referenceScriptLibraries | Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer. |

Utilisez le commutateur `h` pour obtenir de l’aide sur la commande `aspnet-codegenerator razorpage` :

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Pour plus d’informations, voir [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
