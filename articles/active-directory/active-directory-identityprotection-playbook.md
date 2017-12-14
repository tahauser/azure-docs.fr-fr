---
title: "Manuel d’Azure Active Directory Identity Protection | Microsoft Docs"
description: "Découvrez comment Azure AD Identity Protection vous permet de limiter la capacité d’un cybercriminel à exploiter une identité ou un appareil compromis et de sécuriser une identité ou un appareil déjà identifié comme potentiellement ou effectivement compromis."
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: e274a9364dcb7bf4d220159e1f906bbd015369aa
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Manuel d’Azure Active Directory Identity Protection
Ce manuel vous aide à :

* Remplir les données dans l’environnement d’Identity Protection en simulant par des vulnérabilités et des événements à risque
* Définir des stratégies d’accès conditionnel en fonction des risques et tester l’impact de ces stratégies

## <a name="simulating-risk-events"></a>Simulation des événements à risque
Cette section vous fournit les étapes requises pour simuler des types d’événements à risque suivants (le niveau de difficulté est indiqué entre parenthèses) :

* Connexions depuis des adresses IP anonymes (facile)
* Connexions depuis des emplacements non connus (moyen)
* Voyage impossible vers des emplacements inhabituels (difficile)

Les autres événements à risque ne peuvent pas être simulés de manière sécurisée.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Connexions depuis des adresses IP anonymes
Ce type d’événement à risque signale les utilisateurs qui se sont connectés depuis une adresse IP ayant été identifiée comme l’adresse IP d’un proxy anonyme. Ces proxys sont utilisés par des individus souhaitant masquer l’adresse IP de leur appareil et peuvent être utilisés dans un but malveillant.

**Pour simuler une connexion depuis une adresse IP anonyme, procédez comme suit**:

1. Téléchargez le [navigateur Tor](https://www.torproject.org/projects/torbrowser.html.en).
2. À l’aide du navigateur Tor, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3. Entrez les informations d’identification du compte que vous souhaitez voir apparaître dans le rapport **Connexions depuis des adresses IP anonymes** .

La connexion s’affiche dans le tableau de bord d’Identity Protection dans un délai de 5 minutes. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Connexions depuis des emplacements non connus
Le risque lié aux emplacements non connus est déterminé à l’aide d’un mécanisme d’évaluation de connexion en temps réel qui prend en compte les emplacements de connexion passés (IP, latitude/longitude et NSA) pour déterminer les emplacements non connus/nouveaux. Le système stocke les adresses IP, la latitude/longitude et les NSA précédents d’un utilisateur et considère ces emplacements comme connus. Un emplacement de connexion est considéré comme non connu si l’emplacement de connexion ne correspond à aucun emplacement connu existant.

Azure Active Directory Identity Protection :  

* présente une période d’apprentissage initiale de 14 jours, durant laquelle il ne signale pas les nouveaux emplacements en tant qu’emplacements non connus.
* ignore les connexions depuis les appareils connus et les emplacements géographiquement proches d’un emplacement connu existant.

Pour simuler des emplacements non connus, vous devez vous connecter depuis un emplacement et un appareil depuis lesquels le compte ne s’est jamais connecté. 

**Pour simuler une connexion depuis un emplacement non connu, procédez comme suit**:

1. Choisissez un compte qui présente un historique de connexion d’au moins 14 jours. 
2. Effectuez ensuite l’une ou l’autre de ces étapes :
   
   a. Tout en utilisant un VPN, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com) et entrez les informations d’identification du compte pour lequel vous souhaitez simuler cet événement à risque.
   
   b. Demandez à un collaborateur se trouvant à un emplacement différent de se connecter à l’aide des informations d’identification du compte (non recommandé).

La connexion s’affiche dans le tableau de bord d’Identity Protection dans un délai de 5 minutes.

### <a name="impossible-travel-to-atypical-location"></a>Voyage impossible vers des emplacements inhabituels
La simulation de la condition de voyage impossible est difficile, car l’algorithme utilise l’apprentissage automatique pour éliminer les faux positifs, tels que le voyage impossible depuis des appareils connus ou les connexions depuis des VPN utilisés par d’autres utilisateurs du répertoire. De plus, l’algorithme requiert un historique de connexion de 3 à 14 jours pour l’utilisateur avant de commencer à générer des événements à risque.

**Pour simuler un voyage impossible vers des emplacements inhabituels, procédez comme suit**:

1. À l’aide de votre navigateur standard, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Entrez les informations d’identification du compte pour lequel vous souhaitez générer cet événement à risque.
3. Changez votre agent utilisateur. Vous pouvez changer l’agent utilisateur depuis les outils de développement dans Internet Explorer ou à l’aide d’un module complémentaire de sélecteur d’agents utilisateur dans Firefox ou Chrome.
4. Changez votre adresse IP. Vous pouvez changer votre adresse IP en utilisant un VPN, un module complémentaire Tor ou en créant une nouvelle machine au sein d’Azure dans un autre centre de données.
5. Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com) à l’aide des mêmes informations d’identification que précédemment et dans un délai de quelques minutes seulement après la connexion précédente.

La connexion s’affiche dans le tableau de bord d’Identity Protection dans un délai de 2 à 4 heures.<br>
En raison des modèles d’apprentissage automatique complexes impliqués, il se peut qu’elle ne soit pas détectée.<br> Il peut être judicieux de répéter ces étapes pour plusieurs comptes Azure AD.

## <a name="simulating-vulnerabilities"></a>Simulation de vulnérabilités
Les vulnérabilités sont des points faibles exploitables par une personne malveillante au sein de votre environnement Azure AD. Actuellement, Azure AD Identity Protection présente trois types de vulnérabilités tirant parti d’autres fonctionnalités d’Azure AD. Ces vulnérabilités s’affichent automatiquement dans le tableau de bord d’Identity Protection dès lors que ces fonctionnalités sont configurées.

* Azure AD [Multi-Factor Authentication ?](../multi-factor-authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
* Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="user-compromise-risk"></a>Risque de compromission de l’utilisateur
**Pour tester le risque de compromission de l’utilisateur, procédez comme suit**:

1. Connectez-vous à [https://portal.azure.com](https://portal.azure.com) à l’aide des informations d’identification d’administrateur général pour votre client.
2. Accédez à **Identity Protection**. 
3. Dans le panneau principal **d’Azure AD Identity Protection**, cliquez sur **Paramètres**. 
4. Dans le panneau **Paramètres du portail**, sous **Règles de sécurité**, cliquez sur **Risque de compromission de l’utilisateur**. 
5. Dans le panneau **Risque à la connexion**, désactivez l’option **Activer la règle**, puis cliquez sur **Enregistrer**.
6. Pour un compte d’utilisateur donné, simulez un événement à risque lié à des emplacements non connus ou à une adresse IP anonyme. Cela aura pour effet d’élever le niveau de risque de cet utilisateur à **Moyen**.
7. Patientez quelques minutes et vérifiez que le niveau de risque de votre utilisateur est défini sur **Moyen**.
8. Accédez au panneau **Paramètres du portail** .
9. Dans le panneau **Risque de compromission de l’utilisateur**, sélectionnez **Oui** sous **Activer la règle**. 
10. Sélectionnez l’une des options suivantes :
    
    a. Pour bloquer l’accès, sélectionnez **Moyen** sous **Bloquer la connexion**.
    
    b. Pour appliquer un changement de mot de passe sécurisé, sélectionnez **Moyen** sous **Exiger une authentification multifacteur**.
11. Cliquez sur **Save**.
12. Vous pouvez maintenant tester l’accès conditionnel en fonction des risques en vous connectant à l’aide d’un compte d’utilisateur présentant un niveau de risque élevé. Si le risque de l’utilisateur est défini sur Moyen, votre connexion sera bloquée ou vous serez obligé de changer votre mot de passe, selon la configuration de votre stratégie. 
    <br><br>
    ![Manuel](./media/active-directory-identityprotection-playbook/201.png "Manuel")
    <br>

## <a name="sign-in-risk"></a>Risque à la connexion
**Pour tester le risque à la connexion, procédez comme suit :**

1. Connectez-vous à [https://portal.azure.com ](https://portal.azure.com) à l’aide des informations d’identification d’administrateur général pour votre client.
2. Accédez à **Identity Protection**.
3. Dans le panneau principal **d’Azure AD Identity Protection**, cliquez sur **Paramètres**. 
4. Dans le panneau **Paramètres du portail** panneau, sous **Règles de sécurité**, cliquez sur **Risque à la connexion**.
5. Dans le panneau **Risque à la connexion **, sélectionnez **Oui** sous **Activer la règle**. 
6. Sélectionnez l’une des options suivantes :
   
   a. Pour bloquer l’accès, sélectionnez **Moyen** sous **Bloquer la connexion**
   
   b. Pour appliquer un changement de mot de passe sécurisé, sélectionnez **Moyen** sous **Exiger une authentification multifacteur**.
7. Pour bloquer l’accès, sélectionnez Moyen sous Bloquer la connexion.
8. Pour appliquer l’authentification multifacteur, sélectionnez **Moyen** sous **Exiger une authentification multifacteur**.
9. Cliquez sur **Save**(Enregistrer).
10. Vous pouvez maintenant tester l’accès conditionnel en fonction des risques en simulant les événements à risque liés à des emplacements inhabituels ou à des adresses IP anonymes, dont le risque est considéré comme **moyen** .


![Manuel](./media/active-directory-identityprotection-playbook/200.png "Manuel")


## <a name="see-also"></a>Voir aussi
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

