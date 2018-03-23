<!--
Used in:
sql-database-performance-guidance.md 
sql-database-single-database-resources.md 
-->

### <a name="basic-service-tier"></a>Niveau de service De base
| **Niveau de performances** | **De base** |
| :--- | --: |
| DTU max | 5. |
| Espace de stockage inclus (Go) | 2 |
| Choix de stockage maximum (Go) | 2 |
| Stockage In-Memory OLTP maximal (Go) |N/A |
| Nombre maximal d’ouvriers simultanés (demandes) | 30 |
| Nombre maximal de connexions simultanées | 30 |
| Nombre maximal de sessions simultanées | 300 |
|||

### <a name="standard-service-tier"></a>Niveau de service Standard
| **Niveau de performances** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Nombre maximal de DTU** | 10 | 20 | 50 | 100 |
| Espace de stockage inclus (Go) | 250 | 250 | 250 | 250 |
| Choix de l’espace de stockage maximal (Go)* | 250 | 250 | 250 | 250, 500, 750, 1 024 |
| Stockage In-Memory OLTP maximal (Go) | N/A | N/A | N/A | N/A |
| Nombre maximal d’ouvriers simultanés (demandes)| 60 | 90 | 120 | 200 |
| Nombre maximal de connexions simultanées | 60 | 90 | 120 | 200 |
| Nombre maximal de sessions simultanées |600 | 900 | 1 200 | 2 400 |
||||||

### <a name="standard-service-tier-continued"></a>Niveau de service Standard (suite)
| **Niveau de performances** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Nombre maximal de DTU** | 200 | 400 | 800 | 1 600 | 3000 |
| Espace de stockage inclus (Go) | 250 | 250 | 250 | 250 | 250 |
| Choix de l’espace de stockage maximal (Go)* | 250, 500, 750, 1 024 | 250, 500, 750, 1 024 | 250, 500, 750, 1 024 | 250, 500, 750, 1 024 | 250, 500, 750, 1 024 |
| Stockage In-Memory OLTP maximal (Go) | N/A | N/A | N/A | N/A |N/A |
| Nombre maximal d’ouvriers simultanés (demandes)| 400 | 800 | 1 600 | 3200 |6000 |
| Nombre maximal de connexions simultanées | 400 | 800 | 1 600 | 3200 |6000 |
| Nombre maximal de sessions simultanées |4 800 | 9 600 | 19 200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Niveau de service Premium 
| **Niveau de performances** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| DTU max | 125 | 250 | 500 | 1 000 | 1 750 | 4000 |
| Espace de stockage inclus (Go) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Choix de l’espace de stockage maximal (Go)* | 500, 750, 1 024 | 500, 750, 1 024 | 500, 750, 1 024 | 500, 750, 1 024 | 4096 | 4096 |
| Stockage In-Memory OLTP maximal (Go) | 1 | 2 | 4 | 8 | 14 | 32 |
| Nombre maximal d’ouvriers simultanés (demandes)| 200 | 400 | 800 | 1 600 | 2 400 | 6400 |
| Nombre maximal de connexions simultanées | 200 | 400 | 800 | 1 600 | 2 400 | 6 400 |
| Nombre maximal de sessions simultanées | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> \* Les tailles de stockage supérieures à la quantité de stockage inclue sont en version préliminaire et des coûts supplémentaires s’appliquent. Pour en savoir plus, voir [Tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
>
>\* Au niveau Premium, plus de 1 To de stockage est actuellement disponible dans les régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Sud du Brésil, Centre du Canada, Est du Canada, Centre des États-Unis, France-Centre, Centre de l’Allemagne, Est du Japon, Ouest du Japon, Corée Centre, Nord du centre des États-Unis, Europe du Nord, Sud du centre des États-Unis, Sud-Est asiatique, Royaume-Uni Sud, Royaume-Uni Ouest, Est des États-Unis 2, Ouest des États-Unis, Gouvernement des États-Unis – Virginie et Europe de l’Ouest. Consultez [Limitations actuelles P11-P15](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
>\*\* Le nombre maximal de DTU par base de données commençant à 200 DTU et plus en mode Standard est en préversion.
>

