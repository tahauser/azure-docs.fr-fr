Le stockage localement redondant (LRS) est conçu pour fournir une durabilité d’objets d’au moins 99,999999999 % (11 chiffres 9) sur une année donnée en répliquant vos données à l’intérieur d’une unité d’échelle de stockage hébergée dans un centre de données situé dans la région dans laquelle vous avez créé votre compte de stockage. Une demande d’écriture est retournée avec succès uniquement après son écriture dans tous les réplicas. Ces réplicas se trouvent dans des domaines d’erreur et de mise à jour distincts d’une unité d’échelle de stockage.

Une unité d’échelle de stockage est un ensemble de racks de nœuds de stockage. Un domaine d’erreur est un groupe de nœuds qui représentent une unité physique d’incident et peuvent être considérés comme des nœuds appartenant au même rack physique. Un domaine de mise à niveau est un groupe de nœuds qui sont mis à jour ensemble au cours d’un processus de mise à niveau de service (déploiement). Les réplicas sont répartis sur différents domaines d’erreur et de mise à jour au sein d’une unité d’échelle de stockage afin de garantir la disponibilité des données même en cas de défaillance matérielle d’un rack ou quand les nœuds sont mis à niveau pendant un déploiement.

Le stockage LRS est l’option la moins coûteuse et offre une durabilité moindre par rapport aux autres options. En cas d’incident au niveau du centre de données (incendie, inondation, etc.), tous les réplicas risquent d’être perdus ou irrécupérables. Pour atténuer ce risque, un stockage géoredondant (GRS) est recommandé pour la plupart des applications.

Le stockage localement redondant peut toujours être adapté dans certains scénarios :

* Fournit la bande passante maximale la plus élevée de toutes les options de réplication Stockage Azure.
* Si votre application stocke des données qui peuvent être recréées facilement, vous pouvez opter pour un stockage LRS.
* Certaines applications sont limitées à la réplication des données dans un pays en raison des exigences de gouvernance des données. Une région jumelée peut être située dans un autre pays. Pour plus d’informations sur les régions jumelées, voir [Régions Azure](https://azure.microsoft.com/regions/).
