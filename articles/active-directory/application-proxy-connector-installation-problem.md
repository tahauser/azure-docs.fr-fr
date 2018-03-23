---
title: Problèmes lors de l’installation du connecteur d’agent de proxy d’application | Microsoft Docs
description: Comment résoudre les problèmes que vous pouvez rencontrer lors de l’installation du connecteur d’agent de proxy d’application
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 8fbd707b6708661ab0d655afadff2b18694a981e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2018
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problèmes lors de l’installation du connecteur d’agent de proxy d’application

Le connecteur de proxy d’application Microsoft AAD est un composant de domaine interne qui utilise des connexions sortantes pour établir la connectivité à partir du point de terminaison disponible dans le cloud vers le domaine interne.

## <a name="general-problem-areas-with-connector-installation"></a>Problèmes généraux avec l’installation du connecteur

En cas d’échec de l’installation d’un connecteur, la cause est généralement liée à l’un des aspects suivants :

1.  **Connectivité** : pour une installation réussie, le nouveau connecteur doit s’inscrire et établir les propriétés d’approbation ultérieures. Pour cela, connectez-vous au service cloud du proxy d’application AAD.

2.  **Établissement de l’approbation** : le nouveau connecteur crée un certificat auto-signé et s’inscrit auprès du service cloud.

3.  **Authentification de l’administrateur** : pendant l’installation, l’utilisateur doit fournir des informations d’identification d’administrateur pour terminer l’installation du connecteur.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Vérification de la connectivité vers le service de proxy d’application cloud et la page de connexion Microsoft

**Objectif :** vérifier que l’ordinateur connecteur peut se connecter au point de terminaison d’inscription du proxy d’application AAD ainsi qu’à la page de connexion Microsoft.

1.  Ouvrez un navigateur et accédez à la page web suivante : <https://aadap-portcheck.connectorporttest.msappproxy.net>. Ensuite, vérifiez que la connectivité aux centres de données des États-Unis du Centre et des États-Unis de l’Est avec les ports 80 et 443 fonctionne.

2.  En cas d’échec de l’un de ces ports (absence de coche verte), vérifiez que \*.msappproxy.net est correctement défini sur le proxy principal ou le pare-feu avec les ports 80 et 443.

3.  Ouvrez un navigateur (ou un nouvel onglet) et accédez à la page web suivante : <https://login.microsoftonline.com>. Ensuite, vérifiez que vous pouvez vous connecter à cette page.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Vérification de la prise en charge du certificat de confiance du proxy d’application par la machine et les composants principaux

**Objectif :** vérifier que l’ordinateur connecteur, le pare-feu et le proxy principal peuvent prendre en charge le certificat créé par le connecteur en vue d’une approbation ultérieure.

>[!NOTE]
>Le connecteur tente de créer un certificat SHA512 pris en charge par TLS1.2. Si l’ordinateur ou le pare-feu principal et le proxy ne prennent pas en charge TLS1.2, l’installation échoue.
>
>

**Résolution du problème :**

1.  Vérifiez que l’ordinateur prend en charge TLS1.2 (toutes les versions de Windows supérieures à 2012 R2 doivent prendre en charge TLS 1.2). Si votre ordinateur connecteur dispose de la version 2012 R2 ou d’une version antérieure, vérifiez que les bases de connaissances suivantes sont installées sur l’ordinateur : <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Contactez votre administrateur réseau et demandez-lui de vérifier que le proxy principal et le pare-feu ne bloquent par SHA512 pour le trafic sortant.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Vérification de l’utilisation d’une connexion administrateur pour l’installation du connecteur

**Objectif :** vérifier que l’utilisateur qui tente d’installer le connecteur est un administrateur disposant des informations d’identification correctes. Actuellement, l’installation requiert que l’utilisateur soit un administrateur général.

**Pour vérifier que les informations d’identification sont correctes :**

Connectez-vous à <https://login.microsoftonline.com> en utilisant les mêmes informations d’identification. Vérifiez que la connexion a réussi. Vous pouvez vérifier le rôle utilisateur en sélectionnant **Azure Active Directory** -&gt; **Utilisateurs et groupes** -&gt; **Tous les utilisateurs**. 

Sélectionnez votre compte d’utilisateur, puis sélectionnez « Rôle d’annuaire » dans le menu qui s’affiche. Vérifiez que le rôle sélectionné est « Administrateur général ». Si vous ne pouvez accéder à aucune des pages de cette procédure, cela signifie que vous n’êtes pas administrateur général.

## <a name="next-steps"></a>Étapes suivantes
[Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md)
