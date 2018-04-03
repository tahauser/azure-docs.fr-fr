---
title: Gérer Azure Cosmos DB dans l’Explorateur Stockage Azure
description: Découvrez comment gérer Azure Cosmos DB dans l’Explorateur Stockage Azure.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: c593eb2b49d15d20a26ef735d1032d5dea45f125
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-in-storage-explorer-troubleshooting-guide-overview"></a>Présentation du guide de résolution des problèmes de l’Explorateur de stockage Azure Cosmos DB

[Azure Cosmos DB dans l’Explorateur Stockage Azure](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer) est une application autonome qui permet de se connecter aux comptes Azure Cosmos DB hébergés sur Azure et sur les clouds souverains à partir de Windows, Mac OS ou Linux. Elle vous permet de gérer des entités Azure Cosmos DB, de manipuler des données, de mettre à jour des procédures stockées et des déclencheurs, ainsi que d’autres entités Azure comme les files d’attente et les objets blob de stockage.

Ce guide résume les solutions aux problèmes couramment rencontrés dans l’Explorateur de stockage d’Azure Cosmos DB.

- [Problèmes de connexion](#Sign-in-issues)
  - [Certificat auto-signé dans la chaîne d’approbation](#Self-signed-certificate-in-certificate-chain)
  - [Impossible de récupérer les abonnements](#Unable-to-retrieve-subscriptions)
  - [Impossible de voir la page d’authentification](#Unable-to-see-the-authentication-page)
  - [Impossible de supprimer un compte](#Cannot-remove-account)
- [Problème de proxy HTTP/HTTPS](#Http/Https-proxy-issue)
- [Problème de nœud de « Développement » sous le nœud « Local et attaché »](#Development-node-under-Local-and-Attached-node-issue)
- [Erreur lors de l’attachement du compte Azure Cosmos DB dans le nœud « Local et connecté »](#Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error)
- [Erreur de développement du nœud Azure Cosmos DB](#Expand-Azure-Cosmos-DB-node-error)
- [Étapes suivantes](#Next-steps)

<h2 id="Sign-in-issues">Problèmes de connexion</h2>

Avant de continuer, essayez de redémarrer votre application et de voir si les problèmes peuvent être résolus.

<h2 id="Self-signed-certificate-in-certificate-chain">Certificat auto-signé dans la chaîne d’approbation</h2>

Vous pouvez voir cette erreur pour plusieurs raisons. Les deux plus courantes sont :

1. Vous vous trouvez derrière un « proxy transparent », ce qui signifie que quelqu’un (par exemple, votre service informatique) intercepte le trafic HTTPS, le déchiffre, puis le chiffre à l’aide d’un certificat auto-signé.

2. Vous exécutez un logiciel, tel qu’un logiciel antivirus, qui injecte un certificat SSL auto-signé dans les messages HTTPS que vous recevez.

Lorsque l’Explorateur de stockage rencontre l’un de ces « certificats auto-signés », il ne peut plus savoir si le message HTTPS qu’il reçoit a été falsifié. Si vous avez une copie du certificat auto-signé, vous pouvez indiquer à l’Explorateur de stockage de lui faire confiance. Si vous ne savez pas qui injecte le certificat, vous pouvez essayer de le découvrir vous-même en procédant comme suit :

1. Installez Open SSL.
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (n’importe quelle version légère convient)
     - Mac et Linux : doit être inclus dans votre système d’exploitation
2. Exécutez Open SSL.
    - Windows : accédez au répertoire d’installation, puis **/bin/**, puis double-cliquez sur **openssl.exe**.
    - Mac et Linux : exécutez **openssl** à partir d’un terminal
3. Exécutez `s_client -showcerts -connect microsoft.com:443`
4. Recherchez les certificats auto-signés. Si vous ne savez pas lesquels sont auto-signés, recherchez ceux dont le sujet (« s: ») et l’émetteur (« i: ») sont identiques.
5.  Après avoir trouvé les certificats auto-signés, pour chacun d’eux copiez et collez tout depuis **-----BEGIN CERTIFICATE-----** jusqu’à **-----END CERTIFICATE-----** (les deux inclus) dans un nouveau fichier .cer.
6.  Ouvrez l’Explorateur de stockage puis accédez à **Modifier** > **Certificats SSL** > **Importer des certificats**. À l’aide du sélecteur de fichiers, recherchez, sélectionnez et ouvrez les fichiers .cer que vous avez créé.

Si vous ne trouvez aucun certificat auto-signé à l’aide de la procédure ci-dessus, vous pouvez envoyer un commentaire pour obtenir de l’aide.

<h2 id="Unable-to-retrieve-subscriptions">Impossible de récupérer les abonnements</h2>

Si vous ne parvenez pas à récupérer vos abonnements une fois connecté :

- Vérifiez que votre compte a accès aux abonnements en vous connectant au [portail Azure](http://portal.azure.com/).
- Assurez-vous que vous vous êtes connecté à l’aide de l’environnement approprié ([Azure](http://portal.azure.com/), [Azure - Chine](https://portal.azure.cn/), [Azure - Allemagne](https://portal.microsoftazure.de/), [Azure - Gouvernement des États-Unis](http://portal.azure.us/) ou Environnement personnalisé/Azure Stack).
- Si vous vous trouvez derrière un proxy, vérifiez que vous avez correctement configuré le proxy de l’Explorateur de stockage.
- Essayez de supprimer et de rajouter le compte
- Essayez de supprimer les fichiers suivants de votre répertoire de base (par exemple, C:\Users\ContosoUser), puis de rajouter le compte :
  - .adalcache
  - .devaccounts
  - .extaccounts
- Quand vous vous connectez, vérifiez si la console des outils de développement indique des messages d’erreur (en appuyant sur F12).

![console](./media/troubleshoot-cosmosdb/console.png)

<h2 id="Unable-to-see-the-authentication-page">Impossible de voir la page d’authentification</h2>  

Si vous ne parvenez pas à voir la page d’authentification :

- Selon la vitesse de votre connexion, le chargement de la page de connexion peut prendre un certain temps ; attendez au moins une minute avant de fermer la boîte de dialogue d’authentification.
- Si vous vous trouvez derrière un proxy, vérifiez que vous avez correctement configuré le proxy de l’Explorateur de stockage.
- Affichez la console de développement en appuyant sur la touche F12. Dans les réponses indiquées par la console de développement, recherchez des indices éventuels sur la raison du dysfonctionnement de l’authentification.

<h2 id="Cannot-remove-account">Impossible de supprimer un compte</h2>

Si vous ne pouvez pas supprimer un compte, ou que le lien de réauthentification est inopérant

- Essayez de supprimer les fichiers suivants de votre répertoire de base, puis de rajouter le compte :
  - .adalcache
  - .devaccounts
  - .extaccounts
- Si vous souhaitez supprimer des ressources de stockage jointes SAP, supprimez :
  - Dossier %AppData%/StorageExplorer pour Windows
  - /Users/<votre_nom>/Library/Application Support/StorageExplorer pour Mac
  - ~/.config/StorageExplorer pour Linux
  - **Vous devrez entrer à nouveau toutes vos informations d’identification** si vous supprimez ces fichiers.


<h2 id="Http/Https-proxy-issue">Problème de proxy HTTP/HTTPS</h2>

Vous ne pouvez pas afficher les nœuds Azure Cosmos DB dans l’arborescence de gauche lors de la configuration du proxy HTTP/HTTPS dans ASE. Ce problème est connu et sera résolu dans la prochaine version. Pour le moment, vous pouvez utiliser l’Explorateur de données Azure Cosmos DB dans le portail Azure pour contourner ce problème. 

<h2 id="Development-node-under-Local-and-Attached-node-issue">Problème de nœud de « Développement » sous le nœud « Local et attaché »</h2>

Il n’y a pas de réponse après avoir cliqué sur le nœud « Développement » sous le nœud « Local et attaché » dans l’arborescence de gauche.  Ce comportement est attendu. L’émulateur local Azure Cosmos DB sera pris en charge dans la prochaine version.

![Nœud de développement](./media/troubleshoot-cosmosdb/development.png)

<h2 id="Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error">Erreur lors de l’attachement du compte Azure Cosmos DB dans le nœud « Local et attaché »</h2>

Si vous voyez l’erreur ci-dessous après avoir attaché le compte Azure Cosmos DB dans le nœud « Local et attaché», vérifiez si vous utilisez la bonne chaîne de connexion.

![Erreur lors de l’attachement d’Azure Cosmos DB dans « Local et attaché »](./media/troubleshoot-cosmosdb/attached-error.png)

<h2 id="Expand-Azure-Cosmos-DB-node-error">Erreur de développement du nœud Azure Cosmos DB</h2>

L’erreur ci-dessous peut s’afficher lors de la tentative de développement des nœuds dans l’arborescence de gauche. 

![Erreur de développement](./media/troubleshoot-cosmosdb/expand-error.png)

Essayez les suggestions suivantes :

- Vérifiez si le compte Azure Cosmos DB est en cours d’approvisionnement et recommencez une fois le compte créé avec succès.
- Si le compte est sous le nœud « Accès rapide » ou « Local et connecté », vérifiez si le compte a été supprimé. Si c’est le cas, vous devez supprimer le nœud manuellement.

<h2 id="Next-steps">Étapes suivantes</h2>

Si aucune de ces solutions ne vous convient, envoyez un courrier électronique à l’équipe des outils de développement Azure Cosmos DB ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) avec des détails concernant le problème, afin de résoudre les problèmes.





