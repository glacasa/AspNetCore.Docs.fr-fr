
> [!NOTE]
> Pour ce didacticiel, vous utilisez les fonctionnalités de *migrations* Entity Framework Core lorsque c’est possible. Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données. Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées. Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification. Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue. En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite. À la place, vous supprimez puis recréez la base de données quand le schéma change.
>
>Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié. La regénération d’un tableau implique :
>
>* La création d’un nouveau tableau.
>* La copie de données de l’ancien tableau vers le nouveau.
>* La suppression de l’ancien tableau.
>* Renommer la nouvelle table.
>
>Pour plus d’informations, consultez les ressources suivantes :
>
> * [Limites d’un fournisseur de base de données EF Core SQLite](/ef/core/providers/sqlite/limitations)
> * [Personnaliser le code de migration](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Amorçage des données](/ef/core/modeling/data-seeding)
  * [Instruction SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)