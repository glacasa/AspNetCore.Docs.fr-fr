<!-- THIS INCLUDE USED BY MVC AND RP -->
<span data-ttu-id="644d4-101">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="644d4-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="644d4-102">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="644d4-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="644d4-103">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="644d4-103">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="644d4-104">`[DataType(DataType.Date)]`: [L’attribut DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) spécifie le type de données (Date).</span><span class="sxs-lookup"><span data-stu-id="644d4-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date).</span></span> <span data-ttu-id="644d4-105">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="644d4-105">With this attribute:</span></span>

  * <span data-ttu-id="644d4-106">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="644d4-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="644d4-107">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="644d4-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="644d4-108">Les [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="644d4-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>
