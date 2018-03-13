---
title: "Azure AD Connect - mettre à jour le certificat SSL pour une batterie de serveurs AD FS | Microsoft Docs"
description: "Ce document détaille les étapes permettant de mettre à jour le certificat SSL d’une batterie de serveurs AD FS à l’aide d’Azure AD Connect."
services: active-directory
keywords: "azure ad connect, mise à jour de ssl adfs, mise à jour d’un certificat adfs, modifier un certificat adfs, nouveau certificat adfs, certificat adfs, mettre à jour un certificat ssl adf, mettre à jour un certificat adf ssl, configurer un certificat ssl adf, adfs, ssl, certificat, adfs certificat de communication de service, mettre à jour la fédération, configurer la fédération, aad connect"
authors: anandyadavmsft
manager: mtillman
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: anandy
ms.custom: seohack1
ms.openlocfilehash: b31a4d178d287eba275a0072936b4222a2c84346
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Mettre à jour le certificat SSL pour une batterie de serveurs Active Directory Federation Services (AD FS)

## <a name="overview"></a>Vue d’ensemble
Cet article explique comment utiliser Azure AD Connect pour mettre à jour le certificat SSL d’une batterie de serveurs Active Directory Federation Services (AD FS). Vous pouvez utiliser l’outil Azure AD Connect pour une mise à jour facile du certificat SSL pour la batterie de serveurs AD FS même si la méthode d’authentification sélectionnée n’est pas AD FS.

Vous pouvez effectuer l’ensemble de l’opération de mise à jour du certificat SSL pour la batterie de serveurs AD FS sur l’ensemble des serveurs de fédération et Proxy d’application Web (WAP) en trois étapes simples :

![Trois étapes](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>Pour en savoir plus sur les certificats utilisés par AD FS, consultez l’article [Présentation des certificats utilisés par les services ADFS (Active Directory Federation Services)](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>configuration requise

* **Batterie de serveurs AD FS** : Assurez-vous que votre batterie AD FS est basée sur Windows Server 2012 R2 ou version ultérieure.
* **Azure AD Connect** : vérifiez que la version d’Azure AD Connect est 1.1.553.0 ou ultérieure. Vous utiliserez la tâche **Mettre à jour le certificat SSL AD FS**.

![Mettre à jour la tâche SSL](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Étape 1 : Fournir les informations sur la batterie de serveurs AD FS

Azure AD Connect tente d’obtenir automatiquement les informations sur la batterie de serveurs AD FS en effectuant les opérations suivantes :
1. Interrogez les informations de la batterie à partir d’AD FS (Windows Server 2016 ou version ultérieure).
2. Référencer les informations obtenues à partir d’exécutions précédentes stockées localement avec Azure AD Connect.

Vous pouvez modifier la liste des serveurs affichés en ajoutant ou en supprimant des serveurs afin de refléter la configuration actuelle de la batterie de serveurs AD FS. Dès que les informations de serveur sont fournies, Azure AD Connect affiche la connectivité et l’état actuel du certificat SSL.

![Informations du serveur AD FS](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

Si la liste contient un serveur qui ne fait plus partie de la batterie de serveurs AD FS, cliquez sur **Supprimer** pour le supprimer de la liste des serveurs de votre batterie de serveurs AD FS.

![Serveur hors connexion dans la liste](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)

>[!NOTE]
> La suppression d’un serveur dans la liste des serveurs de la batterie de serveurs AD FS dans Azure AD Connect est une opération locale qui met à jour les informations relatives à la batterie de serveurs AD FS qu’Azure AD Connect tient à jour localement. Azure AD Connect ne modifie pas la configuration AD FS pour refléter la modification.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>Étape 2 : Fournir un nouveau certificat SSL

Une fois que vous avez confirmé les informations sur les serveurs de la batterie de serveurs AD FS, Azure AD Connect demande le nouveau certificat SSL. Fournissez un certificat PFX protégé par mot de passe pour poursuivre l’installation.

![Certificat SSL](./media/active-directory-aadconnectfed-ssl-update/certificate.png)

Une fois que vous fournissez le certificat, Azure AD Connect passe par une série de conditions préalables. Vérifiez le certificat pour vous assurer qu’il est correct pour la batterie de serveurs AD FS :

-   Le nom de sujet/l’autre nom de sujet du certificat est identique au nom du service de fédération ou est un certificat à caractères génériques.
-   Le certificat est valide pendant plus de 30 jours.
-   La chaîne d’approbation du certificat est valide.
-   Le certificat est protégé par un mot de passe.

## <a name="step-3-select-servers-for-the-update"></a>Étape 3 : Sélectionner les serveurs concernés par la mise à jour

Dans l’étape suivante, sélectionnez les serveurs qui ont besoin d’avoir le certificat SSL mis à jour. Il est impossible de sélectionner les serveurs qui sont hors connexion pour la mise à jour.

![Sélectionnez les serveurs à mettre à jour](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

Après avoir terminé la configuration, Azure AD Connect affiche le message qui indique l’état de la mise à jour et fournit une option permettant de vérifier l’authentification dans AD FS.

![Configuration terminée](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>FAQ

* **Quel doit être le nom de sujet du nouveau certificat SSL AD FS ?**

    Azure AD Connect vérifie si le nom de sujet/l’autre nom de sujet du certificat contient le nom du service de fédération. Par exemple, si le nom de votre service de fédération est fs.contoso.com, le nom de sujet/l’autre nom de sujet doit être fs.contoso.com.  Les certificats à caractères génériques sont également acceptés.

* **Pourquoi le système me redemande mes informations d’identification dans la page du serveur WAP ?**

    Si les informations d’identification que vous avez fournies pour la connexion aux serveurs AD FS ne disposent également pas du privilège permettant de gérer les serveurs WAP, Azure AD Connect vous demande des informations d’identification bénéficiant du privilège administratif sur les serveurs WAP.

* **Le serveur est indiqué comme étant hors connexion. Que dois-je faire ?**

    Azure AD Connect ne peut effectuer aucune opération si le serveur est hors connexion. Si le serveur fait partie de la batterie de serveurs AD FS, vérifiez la connectivité au serveur. Une fois que vous avez résolu le problème, appuyez sur l’icône d’actualisation pour mettre à jour l’état de l’assistant. Si le serveur faisait déjà partie de la batterie de serveurs et qu’il n’existe plus, cliquez sur **Supprimer** pour le supprimer de la liste des serveurs qu’Azure AD Connect tient à jour. La suppression du serveur de la liste dans Azure AD Connect n’altère pas la configuration AD FS elle-même. Si vous utilisez AD FS dans Windows Server 2016 ou une version ultérieure, le serveur reste dans les paramètres de configuration et s’affiche à nouveau la prochaine fois que la tâche est exécutée.

* **Puis-je mettre à jour une partie des serveurs de ma batterie de serveurs avec le nouveau certificat SSL ?**

    Oui. Vous pouvez toujours exécuter la tâche **Mettre à jour le certificat SSL** pour mettre à jour les serveurs restants. Sur la page **Sélectionner des serveurs pour la mise à jour du certificat SSL**, vous pouvez trier la liste des serveurs sur **Date d’expiration SSL** pour accéder facilement aux serveurs qui ne sont pas encore mis à jour.

* **J’ai supprimé le serveur lors de l’exécution précédente, mais il est toujours affiché comme étant hors connexion et listé dans la page Serveurs AD FS. Pourquoi le serveur hors connexion est-il toujours affiché même après sa suppression ?**

    La suppression du serveur de la liste dans Azure AD Connect ne le supprime pas de la configuration AD FS. Azure AD Connect fait référence à AD FS (Windows Server 2016 ou version ultérieure) pour toutes les informations concernant la batterie de serveurs. Si le serveur est toujours présent dans la configuration AD FS, il sera répertorié dans la liste.  

## <a name="next-steps"></a>Étapes suivantes

- [Fédération avec Azure AD Connect](active-directory-aadconnectfed-whatis.md)
- [Gestion des services AD FS (Active Directory Federation Services) et personnalisation avec Azure AD Connect](active-directory-aadconnect-federation-management.md)
