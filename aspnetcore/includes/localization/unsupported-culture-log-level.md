> [!NOTE]
> Avant ASP.NET Core 3,0, les applications Web écrivent un journal de type `LogLevel.Warning` par demande si la culture demandée n’est pas prise en charge. La journalisation d’une `LogLevel.Warning` par demande peut créer des fichiers journaux volumineux contenant des informations redondantes. Ce comportement a été modifié dans ASP.NET 3,0. La `RequestLocalizationMiddleware` écrit un journal de type `LogLevel.Debug`, ce qui réduit la taille des journaux de production.