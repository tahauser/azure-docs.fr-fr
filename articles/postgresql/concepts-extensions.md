---
title: Utiliser les extensions PostgreSQL dans Azure Database pour PostgreSQL
description: "Décrit la capacité à étendre les fonctionnalités d’une base de données à l’aide des extensions Azure Database pour PostgreSQL."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0b4150fcd7d32c823173c3e2676e226634346a2b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Extensions PostgreSQL dans Azure Database pour PostgreSQL
PostgreSQL offre la possibilité d’étendre les fonctionnalités d’une base de données à l’aide des extensions. Les extensions permettent de regrouper plusieurs objets SQL liés dans un package unique ; elles peuvent être chargées ou supprimées de votre base de données d’une seule commande. Une fois chargées dans la base de données, les extensions peuvent fonctionner comme des fonctionnalités intégrées. Pour plus d’informations sur les extensions PostgreSQL, consultez la page [Empaqueter des objets liés dans une extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Guide pratique pour utiliser les extensions PostgreSQL
Les extensions PostgreSQL doivent être installées dans votre base de données pour être utilisables. Pour installer une extension donnée, exécutez la commande [CRÉER UNE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) dans l’outil psql pour charger les objets empaquetés dans votre base de données.

Azure Database pour PostgreSQL prend actuellement en charge un sous-ensemble d’extensions de clé, comme indiqué ici. Les extensions qui ne sont pas mentionnées ici ne sont pas prises en charge ; vous ne pouvez pas créer votre propre extension avec le service Azure Database pour PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensions prises en charge par Azure Database pour PostgreSQL
Les tables suivantes répertorient les extensions PostgreSQL standard actuellement prises en charge par Azure Database pour PostgreSQL. Ces informations sont également disponibles en interrogeant `pg\_available\_extensions`.

### <a name="data-types-extensions"></a>Extensions de types de données

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Fournit un type de données pour les mots de passe chiffrés automatiquement. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fournit un type de chaîne de caractères avec respect de la casse. |
| [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Fournit un type de données pour les cubes multidimensionnels. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fournit un type de données permettant de stocker des ensembles de paires clé/valeur. |
| [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Fournit des types de données pour les standards internationaux de numérotation de produits. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fournit un type de données pour les structures hiérarchiques de type arborescence. |

### <a name="functions-extensions"></a>Extensions de fonctions

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fournit un moyen de calculer les distances de grands cercles à la surface de la Terre. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Fournit plusieurs fonctions permettant de déterminer les ressemblances et la distance entre des chaînes. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fournit des fonctions et des opérateurs permettant de manipuler des tableaux d’entiers non Null. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fournit des fonctions de chiffrement. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gère les tables partitionnées par date ou par ID. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fournit des fonctions et des opérateurs permettant de déterminer la similarité entre des textes alphanumériques par rapprochement de trigrammes. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fournit des fonctions qui manipulent des tables entières, y compris les tables croisées. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Génère des identificateurs uniques universels (UUID). |

### <a name="full-text-search-extensions"></a>Extensions de recherche en texte intégral

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fournit un modèle de dictionnaire de recherche de texte pour les entiers. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Dictionnaire de recherche de texte qui supprime les accents (signes diacritiques) des lexèmes. |

### <a name="index-types-extensions"></a>Extensions de types d’index

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fournit des exemples de classes d’opérateurs GIN qui implémentent des comportements de type arbre B pour certains types de données. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fournit des classes d’opérateurs d’index GiST qui implémentent l’arbre B. |

### <a name="language-extensions"></a>Extensions de langage

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Langage procédural chargeable PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Extensions diverses

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fournit un moyen d’examiner ce qui se passe dans le cache partagé des tampons en temps réel. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fournit un moyen de charger les données de relation dans le cache des tampons. |
| [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fournit un moyen de suivre les statistiques d’exécution de toutes les instructions SQL exécutées par un serveur. |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fournit un moyen d’afficher des informations de verrouillage au niveau des lignes. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fournit un moyen d’afficher les statistiques au niveau du tuple. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper de données externes permettant d’accéder aux données stockées dans des serveurs externes PostgreSQL. |

### <a name="postgis-extensions"></a>Extensions PostGIS

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objets spatiaux et géographiques pour PostgreSQL. |
| address\_standardizer, address\_standardizer\_data\_us | Utilisé pour analyser une adresse et la décomposer en éléments constitutifs, Utilisé pour prendre en charge l’étape de normalisation des adresses par géocodage. |
| [pgrouting](http://pgrouting.org/) | Étend la base de données géospatiale PostGIS / PostgreSQL pour offrir une fonctionnalité de routage géospatial. |

## <a name="next-steps"></a>Étapes suivantes
Si vous ne voyez pas une extension que vous souhaitez utiliser, faites-le-nous savoir. Votez pour les demandes existantes ou envoyez de nouveaux commentaires et de nouvelles demandes dans notre [Forum de commentaires client](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
