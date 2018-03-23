<!--
Used in:
sql-database-elastic-pool.md 
-->

 
### <a name="basic-elastic-pool-limits"></a>Limites du pool élastique de base

| Nombre d’eDTU par pool | **50** | **100** | **200** | **300** | **400** | **800** | **1 200** | **1 600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Espace de stockage inclus par pool (Go) | 5. | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Choix de l’espace de stockage maximal par pool (Go) | 5. | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Stockage OLTP en mémoire maximal par pool (Go) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A |
| Nombre maximal de bases de données par pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Nombre maximal d’ouvriers simultanés (demandes) par pool | 100 | 200 | 400 | 600 | 800 | 1 600 | 2 400 | 3200 |
| Nombre maximal de connexions simultanées par pool | 100 | 200 | 400 | 600 | 800 | 1 600 | 2 400 | 3200 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Choix du nombre minimal d’eDTU par base de données | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Choix du nombre maximal d’eDTU par base de données | 5. | 5. | 5. | 5. | 5. | 5. | 5. | 5. |
| Espace de stockage maximal par base de données (Go) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Limites du pool élastique standard

| Nombre d’eDTU par pool | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Espace de stockage inclus par pool (Go) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Choix de l’espace de stockage maximal par pool (Go)* | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1 024 | 300, 500, 750, 1 204, 1 280 | 400, 500, 750, 1 024, 1 280, 1 536 | 800, 1 024, 1 280, 1 536, 1 792, 2 048 | 
| Stockage OLTP en mémoire maximal par pool (Go) | N/A | N/A | N/A | N/A | N/A | N/A | 
| Nombre maximal de bases de données par pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Nombre maximal d’ouvriers simultanés (demandes) par pool | 100 | 200 | 400 | 600 | 800 | 1 600 |
| Nombre maximal de connexions simultanées par pool | 100 | 200 | 400 | 600 | 800 | 1 600 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Choix du nombre minimal d’eDTU par base de données | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Choix du nombre maximal d’eDTU par base de données | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Espace de stockage maximal par base de données (Go)* | 500 | 750 | 1 024 | 1 024 | 1 024 | 1 024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limites du pool élastique standard (suite) 

| Nombre d’eDTU par pool | **1 200** | **1 600** | **2 000** | **2 500** | **3 000** |
|:---|---:|---:|---:| ---: | ---: |
| Espace de stockage inclus par pool (Go) | 1 200 | 1 600 | 2000 | 2 500 | 3000 | 
| Choix de l’espace de stockage maximal par pool (Go)* | 1 200, 1 280, 1 536, 1792, 2 048, 2 304, 2 560 | 1 600, 1 792, 2 048, 2 304, 2 560, 2 816, 3 072 | 2 000, 2 048, 2 304, 2 560, 2 816, 3 072, 3 328, 3 584 | 2 500, 2 560, 2 816, 3 072, 3 328, 3 584, 3 840, 4 096 | 3 000, 3 072, 3 328, 3 584, 3 840, 4 096 |
| Stockage OLTP en mémoire maximal par pool (Go) | N/A | N/A | N/A | N/A | N/A | 
| Nombre maximal de bases de données par pool | 500 | 500 | 500 | 500 | 500 | 
| Nombre maximal d’ouvriers simultanés (demandes) par pool | 2 400 | 3200 | 4000 | 5 000 | 6000 |
| Nombre maximal de connexions simultanées par pool | 2 400 | 3200 | 4000 | 5 000 | 6000 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Choix du nombre minimal d’eDTU par base de données | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Choix du nombre maximal d’eDTU par base de données | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Choix de l’espace de stockage maximal par base de données (Go)* | 1 024 | 1 024 | 1 024 | 1 024 | 1 024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Limites du pool élastique Premium

| Nombre d’eDTU par pool | **125** | **250** | **500** | **1 000** | **1 500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Espace de stockage inclus par pool (Go) | 250 | 500 | 750 | 1 024 | 1536 | 
| Choix de l’espace de stockage maximal par pool (Go)* | 250, 500, 750, 1 024 | 500, 750, 1 024 | 750, 1 024 | 1 024 | 1536 |
| Stockage OLTP en mémoire maximal par pool (Go) | 1 | 2 | 4 | 10 | 12 | 
| Nombre maximal de bases de données par pool | 50 | 100 | 100 | 100 | 100 | 
| Nombre maximal d’ouvriers simultanés par pool (demandes) | 200 | 400 | 800 | 1 600 | 2 400 | 
| Nombre maximal de connexions simultanées par pool | 200 | 400 | 800 | 1 600 | 2 400 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Nombre minimal d’eDTU par base de données | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| Nombre maximal d’eDTU par base de données | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Espace de stockage maximal par base de données (Go)* | 1 024 | 1 024 | 1 024 | 1 024 | 1 024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limites du pool élastique Premium (suite) 

| Nombre d’eDTU par pool | **2 000** | **2 500** | **3 000** | **3 500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Espace de stockage inclus par pool (Go) | 2 048 | 2560 | 3 072 | 3 548 | 4096 |
| Choix de l’espace de stockage maximal par pool (Go)* | 2 048 | 2560 | 3 072 | 3 548 | 4096|
| Stockage OLTP en mémoire maximal par pool (Go) | 16 | 20 | 24 | 28 | 32 |
| Nombre maximal de bases de données par pool | 100 | 100 | 100 | 100 | 100 | 
| Nombre maximal d’ouvriers simultanés (demandes) par pool | 3200 | 4000 | 4 800 | 5 600 | 6 400 |
| Nombre maximal de connexions simultanées par pool | 3200 | 4000 | 4 800 | 5 600 | 6 400 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Choix du nombre minimal d’eDTU par base de données | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Choix du nombre maximal d’eDTU par base de données | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Espace de stockage maximal par base de données (Go)* | 1 024 | 1 024 | 1 024 | 1 024 | 1 024 | 
||||||||


> [!IMPORTANT]
> \* Les tailles de stockage supérieures à la quantité de stockage inclue sont en version préliminaire et des coûts supplémentaires s’appliquent. Pour plus d’informations, visitez la [page de tarification du service SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Les tailles de stockage supérieures à la quantité de stockage inclue sont en version préliminaire et des coûts supplémentaires s’appliquent. Pour plus d’informations, visitez la [page de tarification du service SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
>
> \* Au niveau Premium, plus de 1 To de stockage est actuellement disponible dans les régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Sud du Brésil, Centre du Canada, Est du Canada, Centre des États-Unis, France-Centre, Centre de l’Allemagne, Est du Japon, Ouest du Japon, Corée Centre, Nord du centre des États-Unis, Europe du Nord, Sud du centre des États-Unis, Sud-Est asiatique, Royaume-Uni Sud, Royaume-Uni Ouest, Est des États-Unis 2, Ouest des États-Unis, Gouvernement des États-Unis – Virginie et Europe de l’Ouest. Consultez [Limitations actuelles P11-P15](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
>

