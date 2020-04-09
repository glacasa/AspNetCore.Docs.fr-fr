## <a name="share-interop-code-in-a-class-library"></a>Partager le code interop dans une bibliothèque de classe

Le code interop JS peut être inclus dans une bibliothèque de classe, ce qui vous permet de partager le code dans un forfait NuGet.

La bibliothèque de classe gère l’intégration des ressources JavaScript dans l’assemblage construit. Les fichiers JavaScript sont placés dans le dossier *wwwroot.* L’outillage s’occupe de l’intégration des ressources lors de la construction de la bibliothèque.

Le paquet NuGet intégré est référencé dans le fichier de projet de l’application de la même manière que n’importe quel paquet NuGet est référencé. Une fois le paquet restauré, le code de l’application peut appeler dans JavaScript comme s’il s’agissait de C.

Pour plus d’informations, consultez <xref:blazor/class-libraries>.
