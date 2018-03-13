---
title: "Emplacements nommés dans Azure Active Directory | Documents Microsoft"
description: "Découvrez ce que représentent les emplacements nommés et comment les configurer."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b6f80cde24edcbec68309ba033d4da16ee97b731
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="named-locations-in-azure-active-directory"></a>Emplacements nommés dans Azure Active Directory

Les emplacements nommés vous permettent d’étiqueter des plages d’adresses IP approuvées dans votre entreprise. Azure Active Directory utilise des emplacements nommés dans le contexte suivant :

- La détection [d’événements à risque](active-directory-reporting-risk-events.md) pour réduire le nombre de faux positifs reportés.  

- [Accès conditionnel en fonction des emplacements](active-directory-conditional-access-locations.md).


Cet article explique comment configurer des emplacements nommés dans votre environnement.


## <a name="entry-points"></a>Points d’entrée

Vous pouvez accéder à la page de configuration des emplacements nommés dans la section **Sécurité** de la page Azure Active Directory en cliquant sur :

![Points d’entrée](./media/active-directory-named-locations/34.png)

- **Accès conditionnel :**

    - Dans la section **Gérer**, cliquez sur **Emplacements nommés**.
    
        ![Commande Emplacements nommés](./media/active-directory-named-locations/06.png)

- **Connexions risquées :**

    - Dans la barre d’outils supérieure, cliquez sur **Ajouter des plages d’adresses IP connues**.

       ![Commande Emplacements nommés](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Exemple de configuration

**Pour configurer un emplacement nommé :**

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général.

2. Dans le volet gauche, cliquez sur **Azure Active Directory**.

    ![Lien Azure Active Directory dans le volet gauche](./media/active-directory-named-locations/01.png)

3. Dans la page **Azure Active Directory**, dans la section **Sécurité**, cliquez sur **Accès conditionnel**.

    ![Commande Accès conditionnel](./media/active-directory-named-locations/05.png)


4. Dans la page **Accès conditionnel**, dans la section **Gérer**, cliquez sur **Emplacements nommés**.

    ![Commande Emplacements nommés](./media/active-directory-named-locations/06.png)


5. Dans la page **Emplacements nommés**, cliquez sur **Nouvel emplacement**.

    ![Commande Nouvel emplacement](./media/active-directory-named-locations/07.png)


6. Dans la page **Nouveau**, procédez comme suit :

    ![Panneau Nouveau](./media/active-directory-named-locations/56.png)

    a. Dans la zone de texte **Nom**, tapez un nom pour votre emplacement nommé.

    b. Dans la zone de texte **Plage d’adresses IP**, tapez une plage d’adresses IP. La plage d’adresses IP doit être au format *CIDR* (Classless Inter-Domain Routing).  

    c. Cliquez sur **Créer**.



## <a name="what-you-should-know"></a>Ce que vous devez savoir

**Mises à jour en bloc** : lorsque vous créez ou mettez à jour des emplacements nommés, pour effectuer des mises à jour en bloc, vous pouvez charger ou télécharger un fichier CSV contenant les plages d’adresses IP. Un téléchargement ajoute les plages d’adresses IP dans le fichier au lieu de remplacer la liste.

![Liens Charger et Télécharger](./media/active-directory-named-locations/09.png)


**Limites** : vous pouvez définir un maximum de 60 emplacements nommés avec une plage d’adresses IP assignée à chacun d’eux. Si vous n’avez qu’un seul emplacement nommé configuré, vous pouvez définir jusqu’à 500 plages d’adresses IP pour celui-ci.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur :

- **Événements à risque**, consultez [Événements à risque dans Azure Active Directory](active-directory-reporting-risk-events.md).

- **Accès conditionnel**, consultez [Accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- **Rapports des connexions risquées**, consultez [Rapport des connexions risquées dans le portail Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md).  
