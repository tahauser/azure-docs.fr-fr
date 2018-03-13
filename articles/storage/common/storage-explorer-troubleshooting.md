---
title: "Guide de résolution des problèmes de l’Explorateur de stockage Azure | Microsoft Docs"
description: "Vue d’ensemble des deux fonctionnalités de débogage d’Azure"
services: virtual-machines
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: 2f62de428d1915b1e070350a2837f24c3486f8c7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guide de résolution des problèmes de l’Explorateur de stockage Azure

L’Explorateur Stockage Microsoft Azure (préversion) est une application autonome qui vous permet d’utiliser facilement les données Stockage Azure sur Windows, macOS et Linux. L’application peut se connecter aux comptes de stockage hébergés sur Azure, sur les clouds nationaux et sur Azure Stack.

Ce guide résume les solutions aux problèmes couramment rencontrés dans l’Explorateur de stockage.

## <a name="sign-in-issues"></a>Problèmes de connexion

Seuls les comptes Azure Active Directory (AAD) sont pris en charge. Si vous utilisez un compte ADFS, la connexion à l’Explorateur de stockage ne devrait pas fonctionner. Avant de continuer, essayez de redémarrer votre application et de voir si les problèmes peuvent être résolus.

### <a name="error-self-signed-certificate-in-certificate-chain"></a>Erreur : certificat auto-signé dans la chaîne d’approbation

Parmi les raisons pouvant expliquer cette erreur, voici les deux plus courantes :

1. L’application est connectée via un « proxy transparent » ; dans cette configuration, un serveur (par exemple, le serveur de votre société) intercepte le trafic HTTPS, le déchiffre, puis le chiffre à l’aide d’un certificat auto-signé.

2. Vous exécutez une application, telle qu’un logiciel antivirus, qui injecte un certificat SSL auto-signé dans les messages HTTPS que vous recevez.

Quand l’Explorateur de stockage rencontre l’un de ces problèmes, il ne peut plus savoir si le message HTTPS reçu a été falsifié. Si vous avez une copie du certificat auto-signé, vous pouvez laisser l’Explorateur de stockage lui faire confiance. Si vous ne savez pas qui injecte le certificat, suivez ces étapes pour le déterminer :

1. Installez Open SSL.

    - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (n’importe quelle version légère devrait suffire)

    - Mac et Linux : doit être inclus dans votre système d’exploitation

2. Exécutez Open SSL.

    - Windows : ouvrez le répertoire d’installation, cliquez sur **/bin/**, puis double-cliquez sur **openssl.exe**.
    - Mac et Linux : exécutez **openssl** à partir d’un terminal.

3. Exécutez s_client -showcerts -connect microsoft.com:443.

4. Recherchez les certificats auto-signés. Ce sont ceux dont le sujet (« s: ») et l’émetteur (« i: ») sont identiques.

5. Après avoir trouvé les certificats auto-signés pour chacun d’eux, copiez et collez tout depuis **---BEGIN CERTIFICATE---** jusqu’à **---END CERTIFICATE---** (les deux inclus) dans un nouveau fichier .cer.

6. Ouvrez l’Explorateur de stockage, cliquez sur **Modifier** > **Certificats SSL** > **Importer les certificats**, puis utilisez le sélecteur de fichiers pour rechercher, sélectionner et ouvrir les fichiers .cer que vous avez créés.

Si vous ne trouvez aucun certificat auto-signé à l’aide des étapes précédentes, contactez-nous par l’intermédiaire de l’outil de commentaires pour obtenir de l’aide.

### <a name="unable-to-retrieve-subscriptions"></a>Impossible de récupérer les abonnements

Si vous ne parvenez pas à récupérer vos abonnements après vous être connecté avec succès, effectuez les étapes suivantes :

- Vérifiez que votre compte a accès aux abonnements en vous connectant au portail Azure.

- Assurez-vous que vous vous êtes connecté à l’aide de l’environnement approprié (Azure, Azure - Chine, Azure - Allemagne, Azure - Gouvernement des États-Unis ou Environnement personnalisé/Azure Stack).

- Si vous vous trouvez derrière un proxy, vérifiez que vous avez correctement configuré le proxy de l’Explorateur de stockage.

- Essayez de supprimer et de rajouter le compte.

- Essayez de supprimer les fichiers suivants de votre répertoire racine (autrement dit, C:\Users\ContosoUser), puis de rajouter le compte :

    - .adalcache

    - .devaccounts

    - .extaccounts

- Quand vous vous connectez, vérifiez si la console des outils de développement indique des messages d’erreur (en appuyant sur F12) :

![Outils de développement](./media/storage-explorer-troubleshooting/4022501_en_2.png)

### <a name="unable-to-see-the-authentication-page"></a>Impossible de voir la page d’authentification

Si vous ne pouvez pas voir la page d’authentification, effectuez les étapes suivantes :

- Selon la vitesse de votre connexion, le chargement de la page de connexion peut prendre un certain temps ; attendez au moins une minute avant de fermer la boîte de dialogue d’authentification.

- Si vous vous trouvez derrière un proxy, vérifiez que vous avez correctement configuré le proxy de l’Explorateur de stockage.

- Affichez la console de développement en appuyant sur la touche F12. Dans les réponses indiquées par la console de développement, recherchez des indices éventuels sur le dysfonctionnement de l’authentification.

### <a name="cannot-remove-account"></a>Impossible de supprimer un compte

Si vous ne pouvez pas supprimer un compte, ou que le lien de réauthentification est inopérant, effectuez les étapes suivantes :

- Essayez de supprimer les fichiers suivants de votre répertoire racine, puis de rajouter le compte :

    - .adalcache

    - .devaccounts

    - .extaccounts

- Si vous souhaitez supprimer des ressources de stockage jointes SAP, supprimez les fichiers suivants :

    - Dossier %AppData%/StorageExplorer pour Windows

    - /Users/<votre_nom>/Library/Application Support/StorageExplorer pour Mac

    - ~/.config/StorageExplorer pour Linux

> [!NOTE]
>  Après avoir supprimé les fichiers mentionnés précédemment, vous devrez vous reconnecter à vos comptes.

## <a name="proxy-issues"></a>Problèmes de proxy

Tout d’abord, vérifiez que les informations suivantes que vous avez entrées sont toutes correctes :

- URL de proxy et numéro de port

- Nom d’utilisateur et mot de passe si requis par le proxy

### <a name="common-solutions"></a>Solutions courantes

Si vous rencontrez toujours des problèmes, suivez ces étapes :

- Si vous pouvez vous connecter à Internet sans utiliser votre proxy, vérifiez que l’Explorateur de stockage fonctionne sans les paramètres de proxy activés. Si c’est le cas, le dysfonctionnement est peut-être lié à vos paramètres de proxy. Contactez l’administrateur de votre proxy pour identifier les problèmes.

- Vérifiez que les autres applications utilisant le serveur proxy fonctionnent normalement.

- Vérifiez que vous pouvez vous connecter au portail Microsoft Azure à l’aide de votre navigateur web.

- Vérifiez que vous pouvez recevoir des réponses de vos points de terminaison de service. Entrez une des URL de point de terminaison dans votre navigateur. Si vous pouvez vous connecter, vous devez recevoir une réponse XML InvalidQueryParameterValue ou similaire.

- Si quelqu’un d’autre utilise également l’Explorateur de stockage avec votre serveur proxy, vérifiez que cette personne peut se connecter. Si elle le peut, vous devrez peut-être contacter l’administrateur de votre serveur proxy.

### <a name="tools-for-diagnosing-issues"></a>Outils pour diagnostiquer les problèmes

Si vous disposez d’outils de mise en réseau, tels que Fiddler pour Windows, vous pouvez peut-être diagnostiquer les problèmes comme suit :

- Si vous devez passer par votre serveur proxy, vous devez peut-être configurer votre outil de mise en réseau pour vous connecter via le proxy.

- Vérifiez le numéro de port utilisé par votre outil de mise en réseau.

- Entrez l’URL de l’hôte local et le numéro de port de l’outil de mise en réseau en tant que paramètres de proxy dans l’Explorateur de stockage. Si cette opération est effectuée correctement, l’outil de mise en réseau démarre la journalisation des demandes réseau effectuées par l’Explorateur de stockage sur les points de terminaison de service et de gestion. Par exemple, si vous entrez https://cawablobgrs.blob.core.windows.net/ pour votre point de terminaison d’objets blob dans un navigateur, vous recevez une réponse semblable à la suivante, qui suggère que la ressource existe, bien que vous ne puissiez pas y accéder.

![Exemple de code](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Contacter l’administrateur du serveur proxy

Si vos paramètres de proxy sont corrects, vous devrez peut-être contacter l’administrateur de votre serveur proxy, puis :

- Vérifier que votre proxy ne bloque pas le trafic vers les points de terminaison de gestion ou de ressources Azure.

- Vérifier le protocole d’authentification utilisé par votre serveur proxy. L’Explorateur de stockage ne prend pas en charge les proxys NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Message d’erreur indiquant qu’il est impossible de récupérer les enfants

Si vous êtes connecté à Azure par le biais d’un proxy, vérifiez que vos paramètres de proxy sont corrects. Si le propriétaire de l’abonnement ou du compte vous a accordé l’accès à une ressource, vérifiez que vous êtes habilité à lire ou à répertorier cette ressource.

### <a name="issues-with-sas-url"></a>Problèmes liés à l’URL SAP
Si vous vous connectez à un service à l’aide d’une URL SAP et que vous rencontrez cette erreur :

- Vérifiez que l’URL fournit les autorisations nécessaires pour lire ou répertorier les ressources.

- Vérifiez que l’URL n’a pas expiré.

- Si l’URL SAP est basée sur une stratégie d’accès, vérifiez que cette dernière n’a pas été révoquée.

Si vous avez procédé accidentellement à un attachement au moyen d’une URL SAS non valide, sans parvenir ensuite à un détachement, effectuez les étapes suivantes :
1.  Lors de l’exécution de l’Explorateur de stockage, appuyez sur F12 pour ouvrir la fenêtre Outils de développement.
2.  Cliquez sur l’onglet Application, puis cliquez sur Stockage local > file:// dans l’arborescence à gauche.
3.  Recherchez la clé associée au type de service de l’URI SAS problématique. Par exemple, si l’URI SAS incorrect concerne un conteneur d’objets blob, recherchez la clé nommée `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  La valeur de la clé doit être un tableau JSON. Recherchez l’objet associé à l’URI incorrecte, et supprimez-le.
5.  Appuyez sur Ctrl+R pour recharger l’Explorateur de stockage.

## <a name="linux-dependencies"></a>Dépendances Linux

Pour les distributions Linux autres que Ubuntu 16.04, vous devez peut-être installer manuellement quelques dépendances. En général, les packages suivants sont nécessaires :
* libgconf-2-4
* libsecret
* GCC à jour

En fonction de votre distribution, vous devez peut-être installer d’autres packages. Les [Notes de publication](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) de l’Explorateur Stockage contiennent des étapes propres à certaines distributions.

## <a name="next-steps"></a>Étapes suivantes

Si aucune des solutions ne convient, soumettez votre problème au moyen de l’outil de commentaires avec votre e-mail, en indiquant le maximum de détails sur le problème afin que nous puissions vous contacter pour le résoudre.

Pour ce faire, cliquez sur le menu **Aide**, puis sur **Envoyer des commentaires**.

![Commentaires](./media/storage-explorer-troubleshooting/4022503_en_1.png)
