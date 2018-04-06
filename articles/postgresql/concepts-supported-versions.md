---
title: Versions prises en charge dans Azure Database pour PostgreSQL
description: Décrit les versions prises en charge des bases de données Azure pour PostgreSQL.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 1bd0d49ecdfd4f014ccb67409238d9d06ddfb17b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="supported-postgresql-database-versions"></a>Versions prises en charge de la base de données PostgreSQL
Microsoft entend prendre en charge les versions n-2 du moteur PostgreSQL dans le service Azure Database pour PostgreSQL, autrement dit la version principale actuellement publiée (n) et les deux principales versions antérieures (-2).

La base de données Azure pour PostgreSQL prend actuellement en charge les versions suivantes :

## <a name="postgresql-version-962"></a>PostgreSQL version 9.6.2
Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-2.html) pour en savoir plus sur les améliorations et les correctifs dans PostgreSQL 9.6.2.

## <a name="postgresql-version-957"></a>PostgreSQL version 9.5.7
Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-7.html) pour en savoir plus sur les améliorations et les correctifs apportés à PostgreSQL 9.5.7.

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau
La base de données Azure pour PostgreSQL gère automatiquement les correctifs pour les mises à jour de version mineures. Actuellement, la mise à niveau de version principale n’est pas prise en charge. Par exemple, la mise à niveau de PostgreSQL 9.5 à PostgreSQL 9.6 n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la prise en charge des différentes extensions PostgreSQL, consultez la page [Extensions PostgreSQL](concepts-extensions.md)
