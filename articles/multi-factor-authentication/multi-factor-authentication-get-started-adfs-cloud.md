---
title: "Sécurisation des ressources de cloud avec Azure MFA et AD FS | Microsoft Docs"
description: "Voici la page d'authentification multifacteur Azure qui explique la prise en main de l'authentification multifacteur Azure et d’AD FS 2.0 dans le cloud."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 0927fc67-8090-4fdd-913a-b3cfed3fbe77
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: joflore
ms.openlocfilehash: c94b20ec984c96cfb8e7339826ee933ea4194bfc
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Sécurisation des ressources de cloud avec le serveur Azure Multi-Factor Authentication et AD FS
Si votre organisation est fédérée avec Azure Active Directory AD, utilisez l’authentification multifacteur Azure ou les services de fédération d’Active Directory (AD FS) pour sécuriser les ressources auxquelles Azure AD accède. Utilisez les procédures suivantes pour sécuriser les ressources Azure Active Directory avec l’authentification multifacteur Azure ou les services de fédération d’Active Directory.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Sécurisation des ressources Azure AD à l’aide d’AD FS
Pour sécuriser vos ressources de cloud, configurez une règle de revendication afin que les services de fédération Active Directory émettent la revendication multipleauthn lorsqu’un utilisateur effectue la vérification en deux étapes avec succès. Cette revendication est transmise à Azure AD. Suivez cette procédure pour les différentes étapes :


1. Ouvrez Gestion AD FS.
2. Sur la gauche, sélectionnez **Approbations de partie de confiance**.
3. Cliquez avec le bouton droit sur **Plateforme d’identité Microsoft Office 365** et sélectionnez **Modifier les règles de revendication**.

   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)

4. Sous Règles de transformation d’émission, cliquez sur **Ajouter une règle**.

   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)

5. Dans l’Assistant Ajout de règle de revendication de transformation, sélectionnez **Passer ou filtrer une revendication entrante** dans la liste déroulante et cliquez sur **Suivant**.

   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)

6. Nommez votre règle. 
7. Sélectionnez **Références des méthodes d’authentification** pour le type de revendication entrante.
8. Sélectionnez **Transférer toutes les valeurs de revendication**.
    ![Assistant Ajouter une règle de revendication de transformation](./media/multi-factor-authentication-get-started-adfs-cloud/configurewizard.png)
9. Cliquez sur **Terminer**. Fermez la console de gestion AD FS.

## <a name="trusted-ips-for-federated-users"></a>Adresses IP de confiance pour les utilisateurs fédérés
Les adresses IP approuvées permettent aux administrateurs de contourner la vérification en deux étapes pour des adresses IP spécifiques ou pour les utilisateurs fédérés qui ont des requêtes provenant de leur propre intranet. Les sections suivantes décrivent comment configurer des adresses IP approuvées Azure Multi-Factor Authentication avec des utilisateurs fédérés et comment contourner la vérification en deux étapes, lorsqu’une requête provient d’un intranet d’utilisateurs fédérés. Pour cela, vous devez configurer AD FS pour utiliser un passthrough ou filtrer un modèle de revendication entrante avec le type de revendication Dans le périmètre du réseau d’entreprise.

Cet exemple utilise Office 365 pour nos approbations de la partie de confiance.

### <a name="configure-the-ad-fs-claims-rules"></a>Configuration des règles de revendications AD FS
La première chose à faire consiste à configurer les revendications AD FS. Créez deux règles de revendications : une pour le type de revendication Dans le périmètre du réseau d’entreprise et l’autre pour maintenir les utilisateurs connectés.

1. Ouvrez Gestion AD FS.
2. Sur la gauche, sélectionnez **Approbations de partie de confiance**.
3. Cliquez avec le bouton droit sur la **Plateforme d’identité Microsoft Office 365** et sélectionnez **Modifier les règles de revendication…**
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. Sous Règles de transformation d’émission, cliquez sur **Ajouter une règle.**
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. Dans l’Assistant Ajout de règle de revendication de transformation, sélectionnez **Passer ou filtrer une revendication entrante** dans la liste déroulante et cliquez sur **Suivant**.
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Dans la zone en regard du nom de la règle de revendication, nommez votre règle. Par exemple : InsideCorpNet.
7. Dans la liste déroulante, en regard du type de revendication entrante, sélectionnez **Dans le périmètre du réseau d’entreprise**.
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Cliquez sur **Terminer**.
9. Sous Règles de transformation d’émission, cliquez sur **Ajouter une règle**.
10. Dans l’Assistant Ajout de règle de revendication de transformation, sélectionnez **Envoyer les revendications en utilisant une règle personnalisée** dans la liste déroulante et cliquez sur **Suivant**.
11. Dans la zone sous Nom de la règle de revendication : entrez *Keep Users Signed In (Maintenir les utilisateurs connectés)*.
12. Dans la zone Règle personnalisée, entrez :

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Cliquez sur **Terminer**.
14. Cliquez sur **Appliquer**.
15. Cliquez sur **OK**.
16. Fermez Gestion AD FS.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configuration d'adresses IP de confiance Azure Multi-Factor Authentication avec des utilisateurs fédérés
Maintenant que les revendications sont en place, nous pouvons configurer des adresses IP approuvées.

1. Connectez-vous au [Portail Azure](https://portal.com).
2. Sélectionnez **Azure Active Directory** > **Accès conditionnel** > **Emplacements nommés**.
3. À partir du panneau **Accès conditionnel - Emplacements nommés**, sélectionnez **Configurer des adresses IP approuvées MFA**

   ![Azure AD accès conditionnel emplacements nommés Configurer des adresses IP approuvées MFA](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)

4. Sur la page Paramètres du service, sous **Adresses IP approuvées**, sélectionnez **Ignorer l’authentification multifacteur pour les demandes issues d’utilisateurs fédérés provenant de mon intranet**.  
5. Cliquez sur **save**.

Et voilà ! À ce stade, les utilisateurs fédérés d'Office 365 doivent pouvoir utiliser uniquement MFA lorsqu'une revendication provient de l'extérieur de l'intranet de l'entreprise.
