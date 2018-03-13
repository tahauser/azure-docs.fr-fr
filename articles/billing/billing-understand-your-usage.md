---
title: "Comprendre votre utilisation détaillée d’Azure"
description: "Apprenez à lire et à comprendre les sections du fichier CSV sur l’utilisation détaillée de votre abonnement Azure"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: 999f24b5a5c1bd5d259b785f9107697d2928b4eb
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Comprendre les termes figurant sur le fichier détaillant les frais d’utilisation Microsoft Azure 

Le fichier CSV des frais d’utilisation détaillés contient les frais d’utilisation quotidienne et au niveau du compteur pour la période de facturation actuelle. 

Pour obtenir le fichier détaillant l’utilisation, consultez [Comment obtenir votre facture Azure et vos données d’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md).
Il est disponible au format .csv, que vous pouvez ouvrir dans une application de feuille de calcul. Si deux versions sont disponibles, téléchargez la version 2. C’est le format de fichier le plus récent.

Les frais d’utilisation correspondent au total des frais **mensuels** d’un abonnement. Les frais d’utilisation ne prennent pas en compte les crédits et les remises.

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Termes et descriptions détaillés de votre fichier sur l’utilisation détaillée

Les sections suivantes décrivent les termes importants indiqués dans la version 2 du fichier d’utilisation détaillée.

### <a name="statement"></a>Instruction

La partie supérieure du fichier CSV sur l’utilisation détaillée contient les services que vous avez utilisés au cours de la période de facturation du mois. Le tableau suivant répertorie les conditions et les descriptions présentées dans cette section.

| Terme | Description |
| --- | --- |
|Période de facturation |Période de facturation où les compteurs ont été consommés |
|Catégorie du compteur |Identifie le service de niveau supérieur pour l’utilisation |
|Sous-catégorie du compteur |Définit le type de service Azure qui peut affecter le tarif |
|Nom du compteur |Identifie l’unité de mesure du compteur consommé |
|Région du compteur |Identifie l’emplacement du centre de données pour certains services dont le prix est basé sur cet emplacement |
|SKU |Identifie l’identificateur système unique de chaque compteur Azure |
|Unité |Identifie l'unité dans laquelle le service est facturé. Par exemple, Go, heures, 10 000 s. |
|Quantité consommée |Quantité du compteur utilisée au cours de la période de facturation |
|Quantité incluse |Montant du compteur inclus gratuitement dans votre période de facturation actuelle |
|Quantité de dépassement |Affiche la différence entre la quantité consommée et la quantité incluse. Ce montant vous est facturé. Pour les offres avec paiement à l’utilisation n’incluant aucune quantité incluse avec l’offre, ce total est identique à la quantité consommée. |
|Au sein de l'engagement |Affiche les frais liés aux compteurs, déduits du montant de votre engagement associé à votre offre de 6 ou 12 mois. Les frais de compteur sont soustraits dans l’ordre chronologique. |
|Devise |Devise utilisée pour votre période de facturation en cours |
|Dépassement |Affiche les frais liés aux compteurs qui excèdent le montant de votre engagement associé à votre offre de 6 ou 12 mois. |
|Taux d'engagement |Affiche le taux d’engagement basé sur le montant total de votre engagement associé à votre offre de 6 ou 12 mois. |
|Tarif |Montant qui vous est facturé par unité facturable |
|Valeur |Affiche le résultat de la multiplication de la colonne Quantité de dépassement par la colonne Taux. Si la quantité consommée ne dépasse pas la quantité incluse, aucun frais n’apparaît dans cette colonne. |

### <a name="daily-usage"></a>Utilisation quotidienne

La section Utilisation quotidienne du fichier CSV montre les détails d’utilisation qui affectent les tarifs de facturation. Le tableau suivant répertorie les conditions et les descriptions présentées dans cette section.

| Terme | Description |
| --- | --- |
|Date d'utilisation |Date à laquelle le compteur a été utilisé |
|Catégorie du compteur |Identifie le service de niveau supérieur dont cette utilisation relève |
|ID du compteur |Identificateur de compteur facturé pour l’établissement du prix de l’utilisation de la facturation |
|Sous-catégorie du compteur |Définit le type de service Azure pouvant affecter le tarif |
|Nom du compteur |Identifie l’unité de mesure du compteur consommé |
|Région du compteur |Identifie l’emplacement du centre de données pour certains services dont le prix est basé sur cet emplacement |
|Unité |Identifie l’unité dans laquelle le compteur est facturé. Par exemple, Go, heures, 10 000 s. |
|Quantité consommée |Quantité du compteur consommé le jour en question |
|Emplacement de la ressource |Identifie le centre de données dans lequel le compteur est en cours d’exécution |
|Service consommé |Service de plateforme Azure que vous avez utilisé |
|Groupe de ressources |Groupe de ressources dans lequel le compteur déployé est en cours d’exécution. <br/><br/>Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
|ID de l’instance | Identificateur du compteur. <br/><br/> L’identificateur contient le nom que vous avez spécifié pour le compteur lors de sa création. Nom de la ressource ou ID de ressource complet. Pour plus d’informations, consultez la page [API Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). |
|Balises | Balise que vous affectez au compteur. Utilisez des balises pour regrouper les enregistrements de facturation.<br/><br/>Par exemple, vous pouvez utiliser des balises pour répartir les coûts par département qui utilise le compteur. Les services qui prennent en charge l’émission de balises sont les services de machine virtuelle, de stockage et de réseau approvisionnés à l’aide de [l’API Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Informations supplémentaires |Métadonnées relatives au service. Par exemple, le type d’image d’une machine virtuelle. |
|Informations sur le service 1 |Nom du projet auquel le service appartient sur votre abonnement. |
|Informations sur le service 2 |Champ hérité capturant les métadonnées facultatives propres au service |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Comment m’assurer que les frais indiqués dans mon fichier sur l’utilisation détaillée sont corrects ?
Si vous voulez plus de détails sur des frais indiqués sur votre fichier sur l’utilisation détaillée, consultez [Comprendre votre facture Microsoft Azure.](./billing-understand-your-bill.md)

## <a name="external"></a>Quels sont les frais associés aux services externes ?
Les services externes (également appelés commandes de la Place de marché) sont fournis par les fournisseurs de services indépendants et sont facturés séparément. Les frais ne s’affichent pas sur la facture Azure. Pour en savoir plus, consultez [Présentation de vos frais de service externe Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?) pour obtenir une prise en charge rapide de votre problème.
