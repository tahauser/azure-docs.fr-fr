---
title: 'Microsoft Genomics : Questions courantes | Microsoft Docs'
titleSuffix: Azure
description: "Réponses apportées aux questions fréquemment posées par les clients de Microsoft Genomics."
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 2077eeb5177b07c458476ae900f81b72e35f0dc3
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics : Questions courantes

Cet article répertorie les principales questions relatives à Microsoft Genomics. Pour plus d’informations sur le service Microsoft Genomics, voir [Qu’est-ce que Microsoft Genomics ?](overview-what-is-genomics.md) 


## <a name="what-is-the-sla-for-microsoft-genomics"></a>Quel contrat de niveau de service utiliser pour Microsoft Genomics ?
Dans 99,9 % des cas, nous garantissons que le service Microsoft Genomics sera disponible pour recevoir des demandes d’API de workflow. Pour plus d’informations, consultez [Contrat de niveau de service](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Comment l’utilisation de Microsoft Genomics apparaît-elle sur ma facture ?
Les factures Microsoft Genomics indiquent le nombre de gigabases traitées par workflow. Pour plus d’informations, voir la [tarification](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Où puis-je trouver une liste de l’ensemble des commandes et arguments possibles pour le client `msgen` ?
Vous pouvez obtenir une liste complète des commandes et arguments disponibles en exécutant `msgen help`. Si aucun autre argument n’est fourni, vous pouvez vous reporter aux sections d’aide proposées, une pour chaque valeur `submit`, `list`, `cancel` et `status`. Pour obtenir de l’aide sur une commande spécifique, tapez `msgen help command`. Par exemple, `msgen help submit` répertorie toutes les options de type submit.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Quelles sont les commandes les plus couramment utilisées pour le client `msgen` ?
Les commandes les plus couramment utilisées pour le client `msgen` sont des arguments : 

 |**Commande**          |  **Description du champ** |
 |:--------------------|:-------------         |
 |`list`               |Renvoie une liste des travaux soumis. Pour les arguments, consultez `msgen help list`.  |
 |`submit`             |Envoie une requête de workflow au service. Pour les arguments, consultez `msgen help submit`.|
 |`status`             |Renvoie l’état du workflow spécifié par `--workflow-id`. Voir aussi `msgen help status`. |
 |`cancel`             |Envoie une requête d’annulation du traitement du workflow spécifié par `--workflow-id`. Voir aussi `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Où puis-je obtenir la valeur de `--api-url-base` ?
Accédez au portail Azure, puis ouvrez la page de votre compte Microsoft Genomics. Sous le titre **Gestion**, choisissez **Clés d’accès**. Vous y trouvez à la fois l’URL de l’API et vos clés d’accès.

## <a name="where-do-i-get-the-value-for---access-key"></a>Où puis-je obtenir la valeur de `--access-key` ?
Accédez au portail Azure, puis ouvrez la page de votre compte Microsoft Genomics. Sous le titre **Gestion**, choisissez **Clés d’accès**. Vous y trouvez à la fois l’URL de l’API et vos clés d’accès.

## <a name="why-do-i-need-two-access-keys"></a>Pourquoi ai-je besoin de deux clés d’accès ?
Vous avez besoin de deux clés d’accès au cas où vous souhaiteriez les mettre à jour (régénérer) sans interrompre l’utilisation du service. Par exemple, vous voulez mettre à jour la première clé. Dans ce cas, vous basculez tous les nouveaux workflow sur la deuxième clé. Il vous suffit ensuite de patienter le temps que les workflows en cours d’exécution et utilisant la première clé soient terminés. Ce n’est qu’à ce moment-là que vous pouvez mettre à jour la clé.

## <a name="do-you-save-my-storage-account-keys"></a>Enregistrez-vous mes clés de compte de stockage ?
Votre clé de compte de stockage sert à créer des jetons d’accès à court terme pour le service Microsoft Genomics afin de lire les fichiers d’entrée et d’écrire les fichiers de sortie. La durée de vie par défaut d’un jeton est de 48 heures. Vous pouvez modifier la durée du jeton avec l’option `-sas/--sas-duration` de la commande submit. La valeur est exprimée en heures.

## <a name="what-genome-references-can-i-use"></a>Quelle référence au génome puis-je utiliser ?

Voici les références prises en charge :
 |Référence              | Valeur de `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (sans analyse alt) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Comment formater les arguments de ligne de commande dans un fichier de configuration ? 

msgen comprend les fichiers de configuration dont le format est le suivant :
* Toutes les options sont fournies en tant que paires clé-valeur, les valeurs étant séparées des clés par le signe deux-points.
Un espace blanc est ignoré.
* Les lignes commençant par `#` sont ignorées.
* Tous les arguments de ligne de commande au format long peuvent être convertis en une clé en supprimant les tirets du début et en remplaçant les tirets entre les mots par des traits de soulignement. Voici quelques exemples de conversion :

 |Argument de ligne de commande            | Ligne du fichier de configuration |
 |:-------------                   |:-------------                 |
 |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
 |`-k/--access-key KEY`            | *access_key:KEY*              |      
 |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Étapes suivantes

Utilisez les ressources suivantes pour prendre en main Microsoft Genomics :
- Commencez par exécuter votre premier workflow via le service Microsoft Genomics. [Exécuter un workflow via le service Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Envoyer vos propres données pour traitement par le service Microsoft Genomics : [FASTQ couplé](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Plusieurs FASTQ ou BAM](quickstart-input-multiple.md) 

