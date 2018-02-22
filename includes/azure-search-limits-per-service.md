Le stockage est limité par l’espace disque ou le *nombre maximum* d’index ou de documents inconditionnel, selon la limite atteinte en premier.

| Ressource | Gratuit | De base | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Contrat de Niveau de Service (SLA) |Nonn <sup>1</sup> |OUI |OUI |OUI |OUI |OUI |
| Stockage par partition |50 Mo |2 Go |25 Go |100 Go |200 Go |200 Go |
| Partitions par service |N/A |1 |12 |12 |12 |3 <sup>2</sup> |
| Taille de partition |N/A |2 Go |25 Go |100 Go |200 Go |200 Go |
| Réplicas |N/A |3 |12 |12 |12 |12 |
| Nombre maximal d’index |3 |5 <sup>3</sup>|50 |200 |200 |1 000 par partition ou 3 000 par service |
| Nombre maximal d’indexeurs |3 |5 <sup>3</sup>|50 |200 |200 |Aucun indexeur pris en charge |
| Nombre maximal de sources de données |3 |5 <sup>3</sup>|50 |200 |200 |Aucun indexeur pris en charge |
| Nombre maximal de documents <sup>3</sup> |10 000 |1 million |15 millions par partition ou 180 millions par service |60 millions par partition ou 720 millions par service |120 millions par partition ou 1,4 milliard par service |1 million par index ou 200 millions par partition |

<sup>1</sup> Les fonctionnalités de niveau Gratuit et d’évaluation ne sont pas fournies avec des Contrats de niveau de service (SLA). Pour tous les niveaux facturables, les SLA entrent en vigueur lorsque vous approvisionnez une redondance suffisante pour votre service. Un SLA de requête (lecture) requiert au moins deux réplicas. Un SLA de requête et d’indexation (lecture-écriture) nécessite au moins trois réplicas. Le nombre de partitions n’est pas pris en compte dans les SLA. 

<sup>2</sup> S3 HD a une limite inconditionnelle de 3 partitions, ce qui est inférieur à la limite de partition de S3. La limite de partition inférieure est imposée car le nombre d’index pour S3 HD est sensiblement plus élevé. En raison des limites existantes de service pour les ressources de calcul (traitement et stockage) et le contenu (index et documents), la limite de contenu est atteinte en premier.

>[!Important]
> **<sup>3</sup>** À partir fin 2017, les nouveaux services de Recherche Azure étaient configurés à l’aide de configurations de matériel sous-jacent puissant, permettant de modifier certaines limites dans certaines régions (Sud du Brésil, Canada Centre, Inde Centre, Est des États-Unis, Nord-Centre des États-Unis, Europe du Nord, Sud-Centre des États-Unis, Asie du Sud-Est, Royaume-Uni Sud, Europe de l’Ouest, et Ouest des États-Unis) :
>
>* Les services Search de niveau De base et Standard créés après fin 2017 n’ont aucune limite de comptes de document; ils n’ont que des limites de stockage. 
>* Pour les services haute densité S3 créés après fin 2017, la limite à 200 millions de documents par partition a été retirée, mais pas la limite à 1 million.
>* La limite des services De base créés après fin 2017 a augmenté à 15 index, sources de données et indexeurs.
>
>Pour en savoir plus sur les limites qui s’appliquent à un service existant, utilisez le portail Azure pour afficher les informations de limite la page Présentation de votre service.