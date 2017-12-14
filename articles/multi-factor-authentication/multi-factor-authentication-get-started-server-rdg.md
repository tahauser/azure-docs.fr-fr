---
title: RDG et Azure MFA Server avec RADIUS | Microsoft Docs
description: "Il s'agit de la page d'authentification multifacteur Azure qui facilite le déploiement de la passerelle Bureau à distance (RD) et le serveur Azure Multi-Factor Authentication à l’aide de RADIUS."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: f2354ac4-a3a7-48e5-a86d-84a9e5682b42
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/27/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 25ccd54599ce73d2fc24febd98ba1042407b1afc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Passerelle des services Bureau à distance et serveur Multi-Factor Authentication avec RADIUS
Souvent, la passerelle du Bureau à distance (RD) utilise les services de stratégie réseau (NPS) locaux pour authentifier les utilisateurs. Cet article explique comment acheminer la demande RADIUS hors de la passerelle Bureau à distance (via le serveur NPS local) vers le serveur Multi-Factor Authentication. La combinaison d’Azure MFA et de la passerelle RD signifie que les utilisateurs peuvent accéder à leur environnement de travail à partir de n’importe quel emplacement, tout en exigeant une authentification forte. 

Étant donné que l’authentification Windows pour Terminal Services n’est pas prise en charge pour Server 2012 R2, utilisez la passerelle RD et RADIUS pour l’intégration au serveur MFA. 

Installez le serveur Multi-Factor Authentication sur un serveur distinct, qui envoie par proxy la demande RADIUS au serveur NPS sur le serveur de la passerelle Bureau à distance. Une fois que le serveur NPS (Network Policy Server) a validé le nom d’utilisateur et le mot de passe, il renvoie une réponse au serveur Multi-Factor Authentication. Ensuite, le serveur MFA applique le second facteur d’authentification et retourne un résultat à la passerelle.

## <a name="prerequisites"></a>Composants requis

- Un serveur Azure MFA joint à un domaine. Si vous n’en avez pas déjà installé un, suivez les étapes décrites dans [Prise en main du serveur Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md).
- Une passerelle Bureau à distance qui s’authentifie avec les services de stratégie réseau.

## <a name="configure-the-remote-desktop-gateway"></a>Configurer la passerelle Bureau à distance
Configurez la passerelle RD pour envoyer l’authentification RADIUS à un serveur Azure Multi-Factor Authentication. 

1. Dans le gestionnaire de passerelle RD, cliquez avec le bouton droit sur le nom du serveur et sélectionnez **Propriétés**.
2. Accédez à l’onglet **Magasin des stratégies d’autorisation des connexions aux services Bureau à distance** et sélectionnez **Serveur central exécutant NPS**. 
3. Ajoutez un ou plusieurs serveurs Azure Multi-Factor Authentication comme serveurs RADIUS en entrant le nom et l’adresse IP de chaque serveur. 
4. Créez un secret partagé pour chaque serveur.

## <a name="configure-nps"></a>Configuration NPS
La passerelle Bureau à distance utilise NPS pour envoyer la demande RADIUS à Azure Multi-Factor Authentication. Pour configurer NPS, vous devez tout d’abord modifier les paramètres de délai d’expiration pour éviter que la passerelle RD n’expire avant que la vérification en deux étapes ait été effectuée. Ensuite, mettez à jour NPS pour recevoir des authentifications RADIUS à partir de votre serveur MFA. Utilisez la procédure suivante pour configurer NPS :

### <a name="modify-the-timeout-policy"></a>Modifier la stratégie d’expiration

1. Dans NPS, ouvrez le menu **Clients et serveurs RADIUS** dans la colonne de gauche, puis sélectionnez **Groupes de serveurs RADIUS distants**. 
2. Sélectionnez le **GROUPE DE SERVEURS DE PASSERELLE TS**. 
3. Accédez à l’onglet **Équilibrage de la charge**. 
4. Modifiez le **nombre de secondes sans réponse avant que la requête est considérée comme supprimée** et le **nombre de secondes entre les demandes lorsque le serveur est identifié comme non disponible** sur une valeur comprise entre 30 et 60 secondes. (Si vous trouvez que le serveur arrive toujours à expiration lors de l’authentification, vous pouvez revenir à cette étape et augmenter le nombre de secondes.)
5. Accédez à l’onglet **Authentification/Compte** et assurez-vous que les ports RADIUS spécifiés correspondent aux ports sur lesquels le serveur Multi-Factor Authentication écoute.

### <a name="prepare-nps-to-receive-authentications-from-the-mfa-server"></a>Préparer NPS à recevoir les authentifications du serveur MFA

1. Cliquez avec le bouton droit sur **Clients RADIUS** sous Clients et serveurs RADIUS dans la colonne de gauche, puis sélectionnez **Nouveau**.
2. Ajoutez le serveur Azure Multi-Factor Authentication en tant que client RADIUS. Choisissez un nom convivial et spécifiez un secret partagé.
3. Ouvrez le menu **Stratégies** dans la colonne de gauche et sélectionnez **Stratégies de demande de connexion**. Vous devez voir une stratégie appelée STRATÉGIE D’AUTORISATION DE PASSERELLE TS créée lors de la configuration de la passerelle RD. Cette stratégie transfère les demandes RADIUS au serveur Azure Multi-Factor Authentication.
4. Cliquez avec le bouton droit sur **STRATÉGIE D’AUTORISATION DE PASSERELLE TS** et sélectionnez **Dupliquer la stratégie**. 
5. Ouvrez la nouvelle stratégie et accédez à l’onglet. **Conditions**.
6. Ajoutez une condition qui met en correspondance le nom convivial du client et le nom convivial défini à l’étape 2 pour le client RADIUS du serveur Azure Multi-Factor Authentication. 
7. Accédez à l’onglet **Paramètres** et sélectionnez **Authentification**.
8. Modifiez le fournisseur d’authentification sur **Authentifier les demandes sur ce serveur**. Cette stratégie garantit que lorsque NPS reçoit une demande RADIUS du serveur Azure MFA, l’authentification se produit localement au lieu de renvoyer une demande RADIUS au serveur Azure MFA, ce qui entraînerait une condition de boucle. 
9. Pour éviter une condition de boucle, assurez-vous que la nouvelle stratégie est placée AU-DESSUS de la stratégie d’origine dans le volet **Stratégies de demande de connexion**.

## <a name="configure-azure-multi-factor-authentication"></a>Configuration d’Azure Multi-Factor Authentication

Le serveur Azure Multi-Factor Authentication est configuré en tant que proxy RADIUS entre la passerelle Bureau à distance et le serveur NPS.  Il devrait être installé sur un serveur appartenant à un domaine, distinct du serveur de la passerelle Bureau à distance. Utilisez la procédure suivante pour configurer le serveur Azure Multi-Factor Authentication.

1. Ouvrez le serveur Azure Multi-Factor Authentication, puis sélectionnez l’icône de l’authentification RADIUS. 
2. Cochez la case **Activer l'authentification RADIUS**.
3. Sous l’onglet Clients, vérifiez que les ports correspondent à ce qui est configuré dans NPS, puis sélectionner **Ajouter**.
4. Ajoutez l’adresse IP du serveur de la passerelle RD, le nom de l’application (facultatif) et un secret partagé. Le secret partagé doit être identique sur le serveur Azure Multi-Factor Authentication et sur la passerelle RD.
3. Accédez à l’onglet **Cible**, puis sélectionnez le bouton radio des **serveurs RADIUS**.
4. Sélectionnez **Ajouter** et entrez l’adresse IP, le secret partagé et les ports du serveur NPS. À moins d’utiliser un NPS central, le client RADIUS et la cible RADIUS seront identiques. Le secret partagé doit correspondre à celui configuré dans la section du client RADIUS du serveur NPS.

![Authentification RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="next-steps"></a>Étapes suivantes

- Intégrer Azure MFA et les [applications web IIS](multi-factor-authentication-get-started-server-iis.md)

- Obtenez des réponses en consultant le [Forum Aux Questions d’Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)
