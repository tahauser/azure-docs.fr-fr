---
title: "Intégration au centre de données Azure Stack - Sécurité"
description: "Découvrez comment intégrer la sécurité Azure Stack à la sécurité de votre centre de données"
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 8ce9045a3e4fd12d61e9b1600ee98880762bc544
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="azure-stack-datacenter-integration---security"></a>Intégration au centre de données Azure Stack - Sécurité
Azure Stack a été conçu et créé avec la sécurité comme préoccupation. Azure Stack est un système de verrouillage : l’installation de l’agent de sécurité logiciel n’est pas donc prise en charge.

Cet article vous permet d’intégrer les fonctionnalités de sécurité d’Azure Stack aux solutions de sécurité déjà déployées dans votre centre de données.

## <a name="security-logs"></a>Journaux de sécurité

Azure Stack collecte les événements du système d’exploitation et de sécurité pour les rôles d’infrastructure et les nœuds d’unités d’échelle toutes les deux minutes. Les journaux sont stockés dans des conteneurs d’objets blob d’un compte de stockage.

Il existe un compte de stockage par rôle d’infrastructure et un compte de stockage pour tous les événements classiques du système d’exploitation.

Le fournisseur de ressources d’intégrité peut être appelé via le protocole REST pour récupérer l’URL dans le conteneur d’objets blob. Les solutions de sécurité de tiers peuvent utiliser les comptes d’API et de stockage pour récupérer les événements à traiter.

### <a name="use-azure-storage-explorer-to-view-events"></a>Utiliser l’Explorateur Stockage Azure pour afficher les événements

Vous pouvez récupérer les événements collectés par Azure Stack en utilisant un outil appelé Explorateur Stockage Azure. Vous pouvez télécharger Explorateur Stockage Azure depuis le site web [http://storageexplorer.com](http://storageexplorer.com).

La procédure suivante est un exemple que vous pouvez utiliser pour configurer Explorateur Stockage Azure pour Azure Stack :

1. Connectez-vous au portail d’administration d’Azure Stack en tant qu’opérateur.
2. Accédez à **Comptes de stockage** et recherchez **frphealthaccount**. Le compte **frphealthaccount** est le compte de stockage général utilisé pour stocker tous les événements du système d’exploitation.

   ![Comptes de stockage](media/azure-stack-integrate-security/storage-accounts.png)

3. Sélectionnez **frphealthaccount** et cliquez sur **Clés d’accès**.

   ![Clés d’accès](media/azure-stack-integrate-security/access-keys.png)

4. Copiez la clé d’accès dans le Presse-papiers.
5. Ouvrez l’Explorateur de stockage Azure.
6. Dans le menu **Modifier**, sélectionnez **Cibler Azure Stack**.
7. Sélectionnez **Ajouter un compte**, puis sélectionnez **Utiliser un nom et une clé de compte de stockage**.

   ![Connecter le stockage](media/azure-stack-integrate-security/connect-storage.png)

8. Cliquez sur **Suivant**.
9. Dans la page **Attacher un stockage externe** :

   a. Tapez le nom du compte **frphealthaccount**.

   b. Collez la clé d’accès du compte de stockage.

   c. Sous **Domaine des points de terminaison de stockage**, sélectionnez **Autre** et spécifiez le point de terminaison de stockage **[Région].[Nom_domaine]**.

   d. Cochez la case **Utiliser HTTP**.

   ![Attacher un stockage externe](media/azure-stack-integrate-security/attach-storage.png)

10. Cliquez sur **Suivant**, passez en revue le récapitulatif et **terminez** l’Assistant.
11. Vous pouvez maintenant parcourir les conteneurs d’objets blob individuels et télécharger les événements.

   ![Parcourir les objets blob](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Utiliser des langages de programmation pour accéder aux événements

Vous pouvez utiliser différents langages de programmation pour accéder à un compte de stockage. Pour obtenir un exemple correspondant à votre langage, utilisez la documentation suivante :

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Audit des accès aux appareils

Tous les appareils physiques dans Azure Stack prennent en charge l’utilisation de TACACS ou de RADIUS. Ceci inclut l’accès au contrôleur BMC (Baseboard Management Controller) et aux commutateurs réseau.

Les solutions Azure Stack ne sont pas fournies avec RADIUS ou TACACS intégrés. Les solutions ont cependant été validées pour prendre en charge l’utilisation de solutions RADIUS ou TACACS existantes disponibles sur le marché.

Pour RADIUS seulement, MSCHAPv2 a été validé. Il représente l’implémentation la plus sécurisée avec RADIUS.
Consultez votre fournisseur de matériel OEM pour activer TACAS ou RADIUS dans les appareils inclus avec votre solution Azure Stack.

## <a name="syslog"></a>syslog

Tous les appareils physiques dans Azure Stack peuvent envoyer des messages Syslog. Les solutions Azure Stack ne sont pas fournies avec un serveur Syslog. Les solutions ont cependant été validées pour prendre en charge l’envoi de messages à des solutions Syslog existantes disponibles sur le marché.

L’adresse de destination Syslog est un paramètre facultatif collecté pour le déploiement, mais il peut également être ajouté après le déploiement.

## <a name="next-steps"></a>Étapes suivantes

[Stratégie de maintenance](azure-stack-servicing-policy.md)
