---
title: "Didacticiel : configurer Workday pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment utiliser Workday en tant que source de données d'identité pour Active Directory et Azure Active Directory."
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/26/2018
ms.author: asmalser
ms.openlocfilehash: 2db9e60fe2807b1aa8ed7cab7eed6f7db8059a89
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Didacticiel : configurer Workday pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de vous présenter les étapes à suivre pour importer des contacts à partir de Workday dans Active Directory et Azure Active Directory, avec écriture différée facultative de certains attributs dans Workday. 



## <a name="overview"></a>Vue d'ensemble

Le [service d’approvisionnement utilisateur Azure Active Directory](active-directory-saas-app-provisioning.md) s’intègre aux [API de ressources humaines Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) afin d’approvisionner des comptes d’utilisateur. Azure AD utilise cette connexion pour activer les flux de travail d’approvisionnement de l’utilisateur suivants :

* **Approvisionnement d’utilisateurs dans Active Directory** : synchroniser des ensembles de d’utilisateurs sélectionnés à partir de Workday dans une ou plusieurs forêts Active Directory. 

* **Approvisionnement d’utilisateurs « cloud only » dans Azure Active Directory** : les utilisateurs hybrides qui existent dans Active Directory et Azure Active Directory peuvent être approvisionnés dans ce dernier à l’aide de [AAD Connect](connect/active-directory-aadconnect.md). Toutefois, les utilisateurs « cloud only » peuvent être approvisionnés directement à partir de Workday vers Azure Active Directory à l’aide du service d’approvisionnement de l’utilisateur Azure AD.

* **Écriture différée des adresses e-mails à Workday** : le service d’approvisionnement de l’utilisateur Azure AD peut écrire des attributs utilisateur Azure AD en différé dans Workday, telles que l’adresse e-mail.

### <a name="scenarios-covered"></a>Scénarios couverts

Les flux de travail d’approvisionnement de l’utilisateur Workday pris en charge par le service d’approvisionnement de l’utilisateur Azure AD autorisent l’automatisation des scénarios de gestion du cycle de vie des identités et des ressources humaines suivants :

* **Nouvelles embauches** : lorsqu’un nouvel employé est ajouté à Workday, un compte d’utilisateur est automatiquement créé dans Active Directory, Azure Active Directory et, éventuellement, Office 365 et [d’autres applications SaaS prises en charge par Azure AD](active-directory-saas-app-provisioning.md), avec l’écriture différée de l’adresse e-mail pour Workday.

* **Mises à jour du profil et des attributs de l’employé** : lorsqu’un enregistrement d’employé est mis à jour dans Workday (par exemple, le nom, le titre ou le responsable), son compte d’utilisateur est automatiquement mis à jour dans Active Directory, Azure Active Directory et, éventuellement, Office 365 et [d’autres applications SaaS prises en charge par Azure AD](active-directory-saas-app-provisioning.md).

* **Résiliations de contrats d’employé** : lorsque le contrat d’un employé est résilié dans Workday, le compte d’utilisateur est automatiquement désactivé dans Active Directory, Azure Active Directory et, éventuellement, Office 365 et [d’autres applications SaaS prises en charge par Azure AD](active-directory-saas-app-provisioning.md).

* **Employé ré-embauché** : lorsqu’un employé est ré-embauché dans Workday, son ancien compte peut être automatiquement réactivé ou réapprovisionné (selon votre préférence) dans Active Directory, Azure Active Directory et, éventuellement, Office 365 et [d’autres applications SaaS prises en charge par Azure AD](active-directory-saas-app-provisioning.md).


## <a name="planning-your-solution"></a>Planification de votre solution

Avant de commencer l’intégration de Workday, vérifiez les prérequis ci-dessous et lisez les conseils suivants sur la façon de faire correspondre votre architecture Active Directory et vos exigences d’approvisionnement de l’utilisateur avec la ou les solutions fournies par Azure Active Directory.

### <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure AD Premium P1 valide avec un accès administrateur général
* Un client de mise en œuvre de Workday à des fins de test et d’intégration
* Autorisations d’administrateur dans Workday pour créer un utilisateur de l’intégration système et apporter des modifications afin de tester les informations de l’employé
* Pour l’approvisionnement de l’utilisateur dans Active Directory, un serveur appartenant à un domaine exécutant le Service Windows 2012 ou supérieur est requis pour héberger[l’agent de synchronisation local](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](connect/active-directory-aadconnect.md) pour la synchronisation entre Active Directory et Azure AD


### <a name="solution-architecture"></a>Architecture de solution

Azure AD fournit un riche ensemble de connecteurs d’approvisionnement pour vous aider à résoudre la gestion de l’approvisionnement et du cycle de vie des identités de Workday à Active Directory, Azure AD, les applications SaaS et au-delà. Les fonctionnalités que vous allez utiliser et la manière de configurer la solution varient selon l’environnement et les exigences de votre organisation. Dans un premier temps, examinez le nombre d’éléments suivants présents et déployés dans votre organisation :

* Combien de forêts Active Directory sont en cours d’utilisation ?
* Combien de domaines Active Directory sont en cours d’utilisation ?
* Combien d’unités d’organisation (UO) Active Directory sont en cours d’utilisation ?
* Combien de clients Azure Active Directory sont en cours d’utilisation ?
* Existe-t-il des utilisateurs qui doivent être approvisionnés dans Active Directory et Azure Active Directory (par exemple, les utilisateurs « hybrides ») ?
* Existe-t-il des utilisateurs qui doivent être approvisionnés dans Azure Active Directory, mais pas dans Active Directory (par exemple, les utilisateurs « cloud only ») ?
* Les adresses e-mail des utilisateurs doivent-elles être différées dans Workday ?

Une fois que vous disposez des réponses à ces questions, vous pouvez planifier le déploiement de l’approvisionnement de Workday en suivant les instructions ci-dessous.

#### <a name="using-provisioning-connector-apps"></a>Utilisation des applications du connecteur d’approvisionnement

Azure Active Directory prend en charge des connecteurs d’approvisionnement pré-intégrés pour Workday et un grand nombre d’autres applications SaaS. 

Un seul connecteur d’approvisionnement se connecte à l’API d’un système source unique et permet l’approvisionnement des données dans un système cible unique. La plupart des connecteurs d’approvisionnement pris en charge par Azure AD sont destinés à un seul système source et cible (par exemple, Azure AD pour ServiceNow) et peuvent être configurés par l’ajout de l’application en question à partir de la galerie d’applications Azure AD (par exemple, ServiceNow). 

Il existe une relation individuelle entre des instances de connecteurs d’approvisionnement et les instances de l’application dans Azure AD :

| Système source | Système cible |
| ---------- | ---------- | 
| Client Azure AD | Application SaaS |


Toutefois, lorsque vous travaillez avec Workday et Active Directory, il existe plusieurs systèmes source et cible à prendre en considération :

| Système source | Système cible | Notes |
| ---------- | ---------- | ---------- |
| Workday | Forêt Active Directory | Chaque forêt est traitée comme un système cible distinct |
| Workday | Client Azure AD | Selon les besoins pour les utilisateurs « cloud only » |
| Forêt Active Directory | Client Azure AD | Ce flux est maintenant géré par AAD Connect |
| Client Azure AD | Workday | Pour l’écriture différée des adresses e-mail |

Pour faciliter ces flux de travail multiples sur plusieurs systèmes source et cible, Azure AD fournit plusieurs applications de connecteurs d’approvisionnement que vous pouvez ajouter à partir de la galerie d’applications Azure AD :

![Galerie d’applications AAD](./media/active-directory-saas-workday-inbound-tutorial/WD_Gallery.PNG)

* **Approvisionnement Workday dans Active Directory** : cette application facilite l’approvisionnement de comptes utilisateur à partir de Workday vers une forêt Active Directory unique. Si vous disposez de plusieurs forêts, vous pouvez ajouter une instance de cette application à partir de la galerie d’applications Azure AD pour chaque forêt Active Directory que vous devez approvisionner.

* **Approvisionnement de Workday à Azure AD** : alors que AAD Connect est l’outil qui doit être utilisé pour synchroniser les utilisateurs Active Directory vers Azure Active Directory, cette application peut être utilisée pour faciliter l’approvisionnement d’utilisateurs « cloud only » à partir de Workday vers un client Azure Active Directory unique.

* **Écriture différée Workday** : cette application facilite l’écriture différée des adresses e-mail de l’utilisateur à partir d’Azure Active Directory vers Workday.

> [!TIP]
> L’application « Workday » standard est utilisée pour configurer l’authentification unique entre Workday et Azure Active Directory. 

L’installation et la configuration de ces applications de connecteurs d’approvisionnement spéciales sont présentées dans les autres sections de ce didacticiel. Les applications que vous choisissez de configurer dépendent des systèmes à approvisionner, du nombre de forêts Active Directory et de clients Azure AD dans votre environnement.

![Vue d'ensemble](./media/active-directory-saas-workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>Configurer un utilisateur de l’intégration système dans Workday
En général, tous les connecteurs d’approvisionnement de Workday exigent des informations d’identification pour un compte d’intégration système Workday pour se connecter à l’API de ressources humaines Workday. Cette section décrit comment créer un compte intégrateur de système dans Workday.

> [!NOTE]
> Il est possible d’ignorer cette procédure et d’utiliser à la place un compte d’administrateur général Workday en tant que compte d’intégration système. Cette procédure, bien qu’adaptée aux démonstrations, n’est pas recommandée pour les déploiements de production.

### <a name="create-an-integration-system-user"></a>Créer un utilisateur de système d’intégration

**Pour créer un utilisateur de système d’intégration :**

1. Connectez-vous à votre client Workday à l’aide d’un compte d’administrateur. Dans **Workday Workbench**, entrez Create user dans la zone de recherche, puis cliquez sur **Create Integration System User**. 
   
    ![Créer un utilisateur](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Créer un utilisateur")
2. Exécutez la tâche **Create Integration System User** en fournissant un nom d’utilisateur et un mot de passe pour un nouvel utilisateur du système d’intégration.  
 * Laissez l’option **Require New Password at Next Sign In** désactivée, étant donné que cet utilisateur se connectera par programmation. 
 * Laissez la valeur par défaut de 0 pour l’option **Session Timeout Minutes** afin d’éviter que les sessions de l’utilisateur n’expirent prématurément. 
   
    ![Créer un utilisateur de système d’intégration](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Créer un utilisateur de système d’intégration")

### <a name="create-a-security-group"></a>Créer un groupe de sécurité
Pour ce faire, vous devez créer un groupe de sécurité système d’intégration sans contrainte et lui affecter l’utilisateur.

**Pour créer un groupe de sécurité :**

1. Entrez create security group dans la zone de recherche, puis cliquez sur **Create Security Group**. 
   
    ![Créer un groupe de sécurité](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Créer un groupe de sécurité")
2. Exécutez la tâche **Create Security Group**.  
3. Sélectionnez Integration System Security Group—Unconstrained dans la liste déroulante **Type of Tenanted Security Group**.
4. Créez un groupe de sécurité auquel des membres sont ajoutés de manière explicite. 
   
    ![Créer un groupe de sécurité](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Créer un groupe de sécurité")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>Affecter l’utilisateur de système d’intégration au groupe de sécurité

**Pour affecter un utilisateur de système d’intégration :**

1. Entrez edit security group dans la zone de recherche, puis cliquez sur **Edit Security Group**. 
   
    ![Modifier un groupe de sécurité](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Modifier un groupe de sécurité")
2. Recherchez et sélectionnez le nouveau groupe de sécurité d’intégration par nom. 
   
    ![Modifier un groupe de sécurité](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Modifier un groupe de sécurité")
3. Ajoutez le nouvel utilisateur système d’intégration au nouveau groupe de sécurité. 
   
    ![Groupe de sécurité système](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Groupe de sécurité système")  

### <a name="configure-security-group-options"></a>Configurer les options du groupe de sécurité
Cette étape a pour objectif d’accorder des autorisations aux données de travail sécurisées par les stratégies de sécurité du domaine suivantes :


| Opération | Stratégie de sécurité du domaine |
| ---------- | ---------- | 
| Get et Put |  External Account Provisioning |
| Get et Put | Worker Data: Public Worker Reports |
| Get et Put | Worker Data: All Positions |
| Get et Put | Worker Data: Current Staffing Information |
| Get et Put | Worker Data: Business Title on Worker Profile |
| Afficher et modifier | Informations sur l’employé : Adresse e-mail professionnelle |

**Pour configurer les options du groupe de sécurité :**

1. Entrez les stratégies de sécurité de domaine dans la zone de recherche, puis cliquez sur le lien **Domain Security Policies for Functional Area**.  
   
    ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Stratégies de sécurité de domaine")  
2. Recherchez system et sélectionnez la zone fonctionnelle **System** .  Cliquez sur **OK**.  
   
    ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Stratégies de sécurité de domaine")  
3. Dans la liste des stratégies de sécurité de la zone fonctionnelle System, développez **Security Administration** et sélectionnez la stratégie de sécurité de domaine **External Account Provisioning**.  
   
    ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Stratégies de sécurité de domaine")  
4. Cliquez sur **Modifier les autorisations**, puis, sur la page de dialogue **Modifier les autorisations**, ajoutez le nouveau groupe de sécurité à la liste des groupes de sécurité avec des autorisations d’intégration **Get** et **Put**. 
   
    ![Modifier l’autorisation](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Modifier l’autorisation")  
5. Répétez l’étape 1 ci-dessus pour revenir à l’écran de sélection des zones fonctionnelles. Cette fois, recherchez staffing, sélectionnez la **zone fonctionnelle Staffing**, puis cliquez sur **OK**.
   
    ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Stratégies de sécurité de domaine")  
6. Dans la liste des stratégies de sécurité de la zone fonctionnelle Staffing, développez **Worker Data: Staffing** et répétez l’étape 4 ci-dessus pour chacune des stratégies de sécurité restantes :

   * Worker Data: Public Worker Reports
   * Worker Data: All Positions
   * Worker Data: Current Staffing Information
   * Worker Data: Business Title on Worker Profile
   
7. Répétez l’étape 1 ci-dessus pour revenir à l’écran de sélection des zones fonctionnelles. Cette fois, recherchez les **coordonnées**, sélectionnez la zone fonctionnelle Dotation en personnel, puis cliquez sur **OK**.

8.  Dans la liste des stratégies de sécurité de la zone fonctionnelle Dotation en personnel, développez **Informations sur l’employé : coordonnées** et répétez l’étape 4 ci-dessus pour chacune des stratégies de sécurité ci-dessous :

    * Informations sur l’employé : Adresse e-mail professionnelle

    ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Stratégies de sécurité de domaine")  
    
### <a name="activate-security-policy-changes"></a>Activer les modifications de la stratégie de sécurité

**Pour activer les modifications de la stratégie de sécurité :**

1. Entrez activate dans la zone de recherche, puis cliquez sur le lien **Activate Pending Security Policy Changes**. 
   
    ![Activer](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activer") 
2. Commencez la tâche Activate Pending Security Policy Changes en entrant un commentaire à des fins d’audit, puis cliquez sur **OK**. 
   
    ![Activer la sécurité en attente](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activer la sécurité en attente")   
3. Terminez la tâche sur l’écran suivant en cochant la case **Confirmer**, puis cliquez sur **OK**. 
   
    ![Activer la sécurité en attente](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activer la sécurité en attente")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configuration de l’approvisionnement de l’utilisateur de Workday à Active Directory
Suivez ces instructions pour configurer l’approvisionnement du compte d’utilisateur depuis Workday pour chaque forêt Active Directory que vous devez approvisionner.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Partie 1 : ajout de l’application des connecteurs d’approvisionnement et création de la connexion à Workday

**Pour configurer l’approvisionnement Workday dans Active Directory :**

1.  Accédez à <https://portal.azure.com>

2.  Dans la barre de navigation de gauche, sélectionnez **Azure Active Directory**

3.  Cliquez sur **Applications d’entreprise**, puis sur **Toutes les applications**.

4.  Sélectionnez **Ajouter une application**, puis sélectionnez la catégorie **Tous**.

5.  Recherchez **Approvisionnement Workday dans Active Directory** et ajoutez cette application à partir de la galerie.

6.  Une fois l’application ajoutée et l’écran de détails de l’application affiché, sélectionnez **Approvisionnement**

7.  Définissez le **Mode** **Approvisionnement** sur **Automatique**

8.  Fermez la section **Informations d’identification de l’administrateur**, comme suit :

   * **Nom d’utilisateur de l’administrateur** : entrez le nom d’utilisateur du compte de système d’intégration Workday, avec le nom du domaine client ajouté. **Le résultat doit être le suivant : username@contoso4**

   * **Mot de passe administrateur :** entrez le mot de passe du compte système d’intégration Workday

   * **URL du client :**  entrez l’URL du point de terminaison des services web Workday pour votre client. Cela doit ressembler à : https://wd3-impl-services1.workday.com/ccx/service/contoso4, où contoso4 est remplacé par le nom correct de votre client et wd3-impl est remplacé par la chaîne d’environnement appropriée.

   * **Forêt Active Directory :** le « Nom » de votre forêt Active Directory, tel que retourné par l’applet de commande powershell Get-ADForest. Il s’agit généralement d’une chaîne telle que : *contoso.com*

   * **Conteneur Active Directory :** entrez la chaîne de conteneurs qui contient tous les utilisateurs de votre forêt AD. Exemple : *OU = Utilisateurs standard, OU = Utilisateurs, DC = contoso, DC = test*

   * **E-mail de notification :** entrez votre adresse e-mail et cochez la case « Envoyer par e-mail en cas de défaillance ».

   * Cliquez sur le bouton **Tester la connexion**. Si le test de connexion réussit, cliquez sur le bouton **Enregistrer** en haut. En cas d’échec, vérifiez que les informations d’identification Workday sont valides dans Workday. 

![Portail Azure](./media/active-directory-saas-workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>Partie 2 : configuration des mappages d’attributs 

Dans cette section, vous allez configurer le flux des données de l’utilisateur de Workday vers Active Directory.

1.  Dans l’onglet Approvisionnement sous **Mappages**, cliquez sur **Synchroniser les employés Workday sur OnPremises**.

2.  Dans le champ **Portée de l’objet source**, vous pouvez sélectionner les ensembles d’utilisateurs dans Workday qui doivent se trouver dans la portée pour l’approvisionnement AD, en définissant un ensemble de filtres basés sur l’attribut. La portée par défaut est « tous les utilisateurs dans Workday ». Exemples de filtres :

   * Exemple : étendue pour les utilisateurs avec des ID d’employés entre 1000000 et 2000000

      * Attribut : WorkerID

      * Opérateur : correspondance REGEX

      * Valeur : (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemple : uniquement les employés et non les employés du contingent 

      * Attribut : EmployeeID

      * Opérateur : n’est pas NULL

3.  Dans le champ **Actions de l’objet cible**, vous pouvez filtrer globalement les actions qui peuvent être exécutées sur Active Directory. Les actions **Créer** et **Mettre à jour** sont les plus courantes.

4.  Dans la section **Mappages d’attributs**, vous pouvez définir comment les attributs Workday sont mappés aux attributs Active Directory.

5. Cliquez sur un mappage d’attributs existants à mettre à jour ou cliquez sur **Ajouter un nouveau mappage** en bas de l’écran pour ajouter de nouveaux mappages. Un mappage d’attribut individuel prend en charge les propriétés suivantes :

      * **Type de mappage**

         * **Direct** : écrit la valeur de l’attribut Workday dans l’attribut AD, sans aucune modification

         * **Constante** : écrivez une valeur de chaîne constante et statique dans l’attribut AD

         * **Expression** : vous permet d’écrire une valeur personnalisée dans l’attribut AD, basée sur un ou plusieurs attributs Workday. [Pour plus d’informations, consultez l’article sur les expressions](active-directory-saas-writing-expressions-for-attribute-mappings.md).

      * **Attribut source** : l’attribut utilisateur dans Workday. Si l’attribut que vous recherchez n’est pas présent, consultez [Personnalisation de la liste des attributs d’utilisateur Workday](#customizing-the-list-of-workday-user-attributes).

      * **Valeur par défaut** : facultatif. Si l’attribut source a une valeur vide, le mappage écrit cette valeur à la place.
            La configuration la plus courante consiste à laisser ce champ vide.

      * **Attribut cible** : l’attribut de l’utilisateur dans Active Directory.

      * **Faire correspondre des objets à l’aide de cet attribut** : indique ci ce mappage est utilisé ou pas pour identifier les utilisateurs de manière unique entre Workday et Active Directory. Cela est généralement défini dans le champ ID de l’employé pour Workday, qui est habituellement mappé sur un des attributs d’ID d’employé dans Active Directory.

      * **Priorité des correspondances** : plusieurs attributs de correspondance peuvent être définis. S’il en existe plusieurs, ils sont évalués dans l’ordre défini par ce champ. Dès qu’une correspondance est trouvée, aucun autre attribut correspondant n’est évalué.

      * **Appliquer ce mappage**
       
         * **Toujours** : appliquez ce mappage à la création de l’utilisateur et aux actions de mise à jour

         * **Lors de la création uniquement** : appliquez ce mappage uniquement aux actions de création utilisateur

6. Pour enregistrer vos mappages, cliquez sur **Enregistrer** en haut de la section Mappage d’attributs.

![Portail Azure](./media/active-directory-saas-workday-inbound-tutorial/WD_2.PNG)

**Vous trouverez ci-dessous quelques exemples de mappages d’attributs entre Workday et Active Directory, avec certaines expressions communes**

-   L’expression qui est mappée à l’attribut parentDistinguishedName AD peut être utilisée pour approvisionner un utilisateur dans une unité d’organisation spécifique basée sur un ou plusieurs attributs source Workday. Cet exemple place les utilisateurs dans différentes unités d’organisation en fonction des données de ville dans Workday.

-   L’expression qui est mappée à l’attribut userPrincipalName AD crée un UPN de firstName.LastName@contoso.com. Elle remplace également des caractères spéciaux non conformes.

-   [Il existe une documentation sur l’écriture d’expressions ici](active-directory-saas-writing-expressions-for-attribute-mappings.md)

  
| ATTRIBUT WORKDAY | ATTRIBUT ACTIVE DIRECTORY |  CORRESPONDANCE D’ID ? | CRÉER / METTRE À JOUR |
| ---------- | ---------- | ---------- | ---------- |
|  **WorkerID**  |  EmployeeID | **Oui** | Écrit lors de la création uniquement | 
|  **Municipalité**   |   l   |     | Créer + mettre à jour |
|  **Société**         | société   |     |  Créer + mettre à jour |
|  **CountryReferenceTwoLetter**      |   co |     |   Créer + mettre à jour |
| **CountryReferenceTwoLetter**    |  c  |     |         Créer + mettre à jour |
| **SupervisoryOrganization**  | department  |     |  Créer + mettre à jour |
|  **PreferredNameData**  |  displayName |     |   Créer + mettre à jour |
| **EmployeeID**    |  cn    |   |   Écrit lors de la création uniquement |
| **Fax**      | facsimileTelephoneNumber     |     |    Créer + mettre à jour |
| **FirstName**   | givenName       |     |    Créer + mettre à jour |
| **Commuter (\[Active\], « 0 », « True », « 1 »,)** |  accountDisabled      |     | Créer + mettre à jour |
| **Mobile**  |    mobile       |     |       Créer + mettre à jour |
| **EmailAddress**    | mail    |     |     Créer + mettre à jour |
| **ManagerReference**   | manager  |     |  Créer + mettre à jour |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Créer + mettre à jour |
| **PostalCode**  |   postalCode  |     | Créer + mettre à jour |
| **LocalReference** |  preferredLanguage  |     |  Créer + mettre à jour |
| **Replace(Mid(Replace(\[EmployeeID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Écrit lors de la création uniquement |
| **LastName**   |   sn   |     |  Créer + mettre à jour |
| **CountryRegionReference** |  st     |     | Créer + mettre à jour |
| **AddressLineData**    |  streetAddress  |     |   Créer + mettre à jour |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Créer + mettre à jour |
| **BusinessTitle**   |  title     |     |  Créer + mettre à jour |
| **Join("@",Replace(Replace(Replace(Replace(Replace(Replace(Replace( Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace( Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Join(".", [FirstName], [LastName]), , "([Øø])", , "oe", , ), , "[Ææ]", , "ae", , ), , "([äãàâãåáąÄÃÀÂÃÅÁĄA])", , "a", , ), , "([B])", , "b", , ), , "([CçčćÇČĆ])", , "c", , ), , "([ďĎD])", , "d", , ), , "([ëèéêęěËÈÉÊĘĚE])", , "e", , ), , "([F])", , "f", , ), , "([G])", , "g", , ), , "([H])", , "h", , ), , "([ïîìíÏÎÌÍI])", , "i", , ), , "([J])", , "j", , ), , "([K])", , "k", , ), , "([ľłŁĽL])", , "l", , ), , "([M])", , "m", , ), , "([ñńňÑŃŇN])", , "n", , ), , "([öòőõôóÖÒŐÕÔÓO])", , "o", , ), , "([P])", , "p", , ), , "([Q])", , "q", , ), , "([řŘR])", , "r", , ), , "([ßšśŠŚS])", , "s", , ), , "([TŤť])", , "t", , ), , "([üùûúůűÜÙÛÚŮŰU])", , "u", , ), , "([V])", , "v", , ), , "([W])", , "w", , ), , "([ýÿýŸÝY])", , "y", , ), , "([źžżŹŽŻZ])", , "z", , ), " ", , , "", , ), "contoso.com")**   | userPrincipalName     |     | Écrit lors de la création uniquement                                                   
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Créer + mettre à jour |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>Partie 3 : configurer l’agent de synchronisation local

Pour configurer Active Directory en local, un agent doit être installé sur un serveur appartenant à un domaine dans la forêt Active Directory désirée. Les informations d’identification administrateur de domaine (ou administrateur d’entreprise) sont requises pour terminer la procédure.

**[Vous pouvez télécharger l’agent de synchronisation local ici](https://go.microsoft.com/fwlink/?linkid=847801)**

Après avoir installé l’agent, exécutez les commandes Powershell ci-dessous pour configurer l’agent pour votre environnement.

**Commande #1**

> cd C:\\Program Files\\Microsoft Azure Active Directory Synchronization Agent\\Modules\\AADSyncAgent

> import-module AADSyncAgent.psd1

**Commande #2**

> Add-ADSyncAgentActiveDirectoryConfiguration

* Entrée : pour « Nom de répertoire », entrez le nom de la forêt AD, tel qu’entré dans la partie \#2
* Entrée : nom d’utilisateur administrateur et mot de passe pour la forêt Active Directory

**Commande #3**

> Add-ADSyncAgentAzureActiveDirectoryConfiguration

* Entrée : nom d’utilisateur administrateur global et mot de passe pour votre client Azure AD

>[!IMPORTANT]
>Il existe actuellement un problème connu avec les informations d’identification d’administrateur global qui ne fonctionnent pas si elles utilisent un domaine personnalisé (exemple : admin@contoso.com). Pour résoudre ce problème, créez et utilisez un compte d’administrateur global avec un domaine onmicrosoft.com (exemple : admin@contoso.onmicrosoft.com)


**Commande #4**

> Get-AdSyncAgentProvisioningTasks

* Action : Vérifiez que les données sont retournées. Cette commande détecte automatiquement les applications d’approvisionnement Workday dans votre client Azure AD. Exemple de sortie :

> Nom : Ma forêt AD
>
> Activé : True
>
> Nom du répertoire : mydomain.contoso.com
>
> Basées sur les références : False
>
> Identificateur : WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**Commande #5**

> Start-AdSyncAgentSynchronization -Automatic

**Commande #6**

> net stop aadsyncagent

**Commande #7**

> net start aadsyncagent

>[!TIP]
>Outre les commandes « net » dans Powershell, le service d’agent de synchronisation peut également être démarré et arrêté à l’aide de **Services.msc**. Si vous obtenez des erreurs lors de l’exécution des commandes PowerShell, vérifiez que **l’agent d’approvisionnement Microsoft Azure AD Connect** s’exécute dans **Services.msc**.

![Services](./media/active-directory-saas-workday-inbound-tutorial/Services.png)  

**Configuration supplémentaire pour les clients de l’Union européenne**

Si votre locataire Azure Active Directory se trouve dans un des centres de données en Europe, suivez les étapes supplémentaires ci-dessous.

1. Ouvrez **Services.msc** et arrêtez le service **Microsoft Azure AD Connect Provisioning Agent** (Agent d’approvisionnement Microsoft Azure AD Connect).
2. Accédez au dossier d’installation de l’agent (exemple : C:\Program Files\Microsoft Azure AD Connect Provisioning Agent).
3. Ouvrez **SyncAgnt.exe.config** dans un éditeur de texte.
4. Remplacez https://manage.hub.syncfabric.windowsazure.com/Management par **https://eu.manage.hub.syncfabric.windowsazure.com/Management**
5. Remplacez https://provision.hub.syncfabric.windowsazure.com/Provisioning par **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning**
6. Enregistrez le fichier **SyncAgnt.exe.config**.
7. Ouvrez **Services.msc** et démarrez le service **Microsoft Azure AD Connect Provisioning Agent** (Agent d’approvisionnement Microsoft Azure AD Connect).

**Résolution des problèmes d’agent**

Le [journal des événements Windows](https://technet.microsoft.com/en-us/library/cc722404(v=ws.11).aspx) sur la machine Windows Server hébergeant l’agent contient des événements pour toutes les opérations effectuées par l’agent. Pour afficher ces événements :
    
1. Ouvrez **Eventvwr.msc**.
2. Sélectionnez **Journaux Windows > Application**.
3. Affichez tous les événements enregistrés sous **l’AADSyncAgent** source. 
4. Consultez les erreurs et avertissements.

En cas de problème lié aux autorisations avec les informations d’identification Active Directory ou Azure Active Directory fournies dans les commandes PowerShell, une erreur comme celle-ci s’affiche : 
    
![Journaux d’événements](./media/active-directory-saas-workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>Partie 4 : démarrer le service
Une fois les parties 1 à 3 terminées, vous pouvez redémarrer le service d’approvisionnement dans le portail Azure.

1.  Dans l’onglet **Approvisionnement**, définissez **État d’approvisionnement** sur **Activé**.

2. Cliquez sur **Enregistrer**.

3. Ceci démarre la synchronisation initiale, qui peut prendre un nombre variable d’heures en fonction du nombre d’utilisateurs dans Workday.

4. À tout moment, consultez l’onglet **Journaux d’audit** dans le portail Azure pour connaître les actions effectuées par le service d’approvisionnement. Les journaux d’audit répertorient tous les événements de synchronisation individuels effectués par le service d’approvisionnement, tels que les utilisateurs lus dans Workday et par la suite ajoutés ou mis à jour dans Active Directory. **[Consultez le guide de création de rapports d’approvisionnement pour obtenir des instructions détaillées sur la façon de lire les journaux d’audit](active-directory-saas-provisioning-reporting.md)**

5.  Consultez le [journal des événements Windows](https://technet.microsoft.com/en-us/library/cc722404(v=ws.11).aspx) sur la machine Windows Server hébergeant l’agent pour connaître les nouveaux avertissements et erreurs. Ces événements sont visibles en lançant **Eventvwr.msc** sur le serveur et en sélectionnant **Journaux Windows > Application**. Tous les messages liés à l’approvisionnement sont enregistrés sous **l’AADSyncAgent** source. 
    

6. Une fois cette opération terminée, un rapport de synthèse de l’audit est écrit dans l’onglet **Approvisionnement**, comme indiqué ci-dessous.

![Portail Azure](./media/active-directory-saas-workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Configuration de l’approvisionnement de l’utilisateur sur Azure Active Directory
La façon dont vous configurez l’approvisionnement vers Azure Active Directory dépend de vos exigences d’approvisionnement, comme indiqué dans le tableau ci-dessous.

| Scénario | Solution |
| -------- | -------- |
| **Les utilisateurs doivent être approvisionnés dans Active Directory et Azure AD** | Utilisez  **[AAD Connect](connect/active-directory-aadconnect.md)** |
| **Les utilisateurs doivent être approvisionnés dans Active Directory uniquement** | Utilisez  **[AAD Connect](connect/active-directory-aadconnect.md)** |
| **Les utilisateurs doivent être configurés dans Azure AD uniquement (« cloud only »)** | Utilisez l’application **Approvisionnement de Workday à Azure Active Directory** dans la galerie d’applications |

Pour obtenir des instructions sur la configuration Azure AD Connect, consultez la [documentation Azure AD Connect](connect/active-directory-aadconnect.md).

Les sections suivantes décrivent la configuration d’une connexion entre Workday et Azure AD pour approvisionner les utilisateurs « cloud only ».

> [!IMPORTANT]
> Ne suivez la procédure ci-dessous que si vous avez des utilisateurs « cloud only » qui doivent être approvisionnés dans Azure AD et pas dans Active Directory local.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Partie 1 : ajout de l’application des connecteurs d’approvisionnement Azure AD et création de la connexion à Workday

**Pour configurer Workday sur l’approvisionnement Azure Active Directory pour les utilisateurs « cloud only » :**

1.  Accédez à <https://portal.azure.com>

2.  Dans la barre de navigation de gauche, sélectionnez **Azure Active Directory**

3.  Cliquez sur **Applications d’entreprise**, puis sur **Toutes les applications**.

4.  Sélectionnez **Ajouter une application**, puis sélectionnez la catégorie **Tous**.

5.  Recherchez **Approvisionnement de Workday vers Azure AD** et ajoutez cette application à partir de la galerie.

6.  Une fois l’application ajoutée et l’écran de détails de l’application affiché, sélectionnez **Approvisionnement**

7.  Définissez le **Mode** **Approvisionnement** sur **Automatique**

8.  Fermez la section **Informations d’identification de l’administrateur**, comme suit :

   * **Nom d’utilisateur de l’administrateur** : entrez le nom d’utilisateur du compte de système d’intégration Workday, avec le nom du domaine client ajouté. Le résultat doit être le suivant : username@contoso4

   * **Mot de passe administrateur :** entrez le mot de passe du compte système d’intégration Workday

   * **URL du client :**  entrez l’URL du point de terminaison des services web Workday pour votre client. Cela doit ressembler à : https://wd3-impl-services1.workday.com/ccx/service/contoso4, où contoso4 est remplacé par le nom correct de votre client et wd3-impl est remplacé par la chaîne d’environnement appropriée. Si cette URL n’est pas connue, collaborez avec votre partenaire d’intégration Workday ou votre représentant du support technique pour déterminer l’URL appropriée à utiliser.

   * **E-mail de notification :** entrez votre adresse e-mail et cochez la case « Envoyer par e-mail en cas de défaillance ».

   * Cliquez sur le bouton **Tester la connexion**.

   * Si le test de connexion réussit, cliquez sur le bouton **Enregistrer** en haut. En cas d’échec, vérifiez que l’URL et les informations d’identification Workday sont valides dans Workday.


### <a name="part-2-configure-attribute-mappings"></a>Partie 2 : configuration des mappages d’attributs 

Dans cette section, vous allez configurer le flux des données de Workday vers Azure Active Directory pour l’utilisateur « cloud only ».

1.  Dans l’onglet Approvisionnement sous **Mappages**, cliquez sur **Synchroniser les employés sur Azure AD**.

2.   Dans le champ **Portée de l’objet source**, vous pouvez sélectionner les ensembles d’utilisateurs dans Workday qui doivent se trouver dans la portée pour l’approvisionnement vers Azure AD, en définissant un ensemble de filtres basés sur l’attribut. La portée par défaut est « tous les utilisateurs dans Workday ». Exemples de filtres :

   * Exemple : étendue pour les utilisateurs avec des ID d’employés entre 1000000 et 2000000

      * Attribut : WorkerID

      * Opérateur : correspondance REGEX

      * Valeur : (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemple : uniquement les employés du contingent et pas les employés réguliers

      * Attribut : ContingentID

      * Opérateur : n’est pas NULL

3.  Dans le champ **Actions de l’objet cible**, vous pouvez filtrer globalement les actions qui peuvent être exécutées sur Azure AD. Les actions **Créer** et **Mettre à jour** sont les plus courantes.

4.  Dans la section **Mappages d’attributs**, vous pouvez définir comment les attributs Workday sont mappés aux attributs Active Directory.

5. Cliquez sur un mappage d’attributs existants à mettre à jour ou cliquez sur **Ajouter un nouveau mappage** en bas de l’écran pour ajouter de nouveaux mappages. Un mappage d’attribut individuel prend en charge les propriétés suivantes :

   * **Type de mappage**

      * **Direct** : écrit la valeur de l’attribut Workday dans l’attribut AD, sans aucune modification

      * **Constante** : écrivez une valeur de chaîne constante et statique dans l’attribut AD

      * **Expression** : vous permet d’écrire une valeur personnalisée dans l’attribut AD, basée sur un ou plusieurs attributs Workday. [Pour plus d’informations, consultez l’article sur les expressions](active-directory-saas-writing-expressions-for-attribute-mappings.md).

   * **Attribut source** : l’attribut utilisateur dans Workday. Si l’attribut que vous recherchez n’est pas présent, consultez [Personnalisation de la liste des attributs d’utilisateur Workday](#customizing-the-list-of-workday-user-attributes).

   * **Valeur par défaut** : facultatif. Si l’attribut source a une valeur vide, le mappage écrit cette valeur à la place.
            La configuration la plus courante consiste à laisser ce champ vide.

   * **Attribut cible** : l’attribut utilisateur dans Azure AD.

   * **Faire correspondre des objets à l’aide de cet attribut** : indique si ce mappage doit être utilisé pour identifier les utilisateurs de manière unique entre Workday et Azure AD. Cela est généralement défini dans le champ ID de l’employé pour Workday, habituellement mappé sur un des attributs d’ID de l’employé (nouveau) dans Azure AD.

   * **Priorité des correspondances** : plusieurs attributs de correspondance peuvent être définis. S’il en existe plusieurs, ils sont évalués dans l’ordre défini par ce champ. Dès qu’une correspondance est trouvée, aucun autre attribut correspondant n’est évalué.

   * **Appliquer ce mappage**

     * **Toujours** : appliquez ce mappage à la création de l’utilisateur et aux actions de mise à jour

     * **Lors de la création uniquement** : appliquez ce mappage uniquement aux actions de création utilisateur

6. Pour enregistrer vos mappages, cliquez sur **Enregistrer** en haut de la section Mappage d’attributs.

### <a name="part-3-start-the-service"></a>Partie 3 : démarrer le service
Une fois les parties 1 à 2 terminées, vous pouvez démarrer le service d’approvisionnement.

1.  Dans l’onglet **Approvisionnement**, définissez **État d’approvisionnement** sur **Activé**.

2. Cliquez sur **Enregistrer**.

3. Ceci démarre la synchronisation initiale, qui peut prendre un nombre variable d’heures en fonction du nombre d’utilisateurs dans Workday.

4. Les événements de synchronisation individuels peuvent être affichés dans l’onglet **Journaux d’audit**. **[Consultez le guide de création de rapports d’approvisionnement pour obtenir des instructions détaillées sur la façon de lire les journaux d’audit](active-directory-saas-provisioning-reporting.md)**

5. Une fois cette opération terminée, un rapport de synthèse de l’audit est écrit dans l’onglet **Approvisionnement**, comme indiqué ci-dessous.


## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Configuration de l’écriture différée des adresses de messagerie à Workday
Suivez ces instructions pour configurer l’écriture différée des adresses e-mail des utilisateurs d’Azure Active Directory vers Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Partie 1 : ajout de l’application des connecteurs d’approvisionnement et création de la connexion à Workday

**Pour configurer l’approvisionnement Workday dans Active Directory :**

1.  Accédez à <https://portal.azure.com>

2.  Dans la barre de navigation de gauche, sélectionnez **Azure Active Directory**

3.  Cliquez sur **Applications d’entreprise**, puis sur **Toutes les applications**.

4.  Sélectionnez **Ajouter une application**, puis sélectionnez la catégorie **Tous**.

5.  Recherchez **Écriture différée Workday** et ajoutez cette application à partir de la galerie.

6.  Une fois l’application ajoutée et l’écran de détails de l’application affiché, sélectionnez **Approvisionnement**

7.  Définissez le **Mode** **Approvisionnement** sur **Automatique**

8.  Fermez la section **Informations d’identification de l’administrateur**, comme suit :

   * **Nom d’utilisateur de l’administrateur** : entrez le nom d’utilisateur du compte de système d’intégration Workday, avec le nom du domaine client ajouté. Le résultat doit être le suivant : username@contoso4

   * **Mot de passe administrateur :** entrez le mot de passe du compte système d’intégration Workday

   * **URL du client :**  entrez l’URL du point de terminaison des services web Workday pour votre client. Cela doit ressembler à : https://wd3-impl-services1.workday.com/ccx/service/contoso4, où contoso4 est remplacé par le nom correct de votre client et wd3-impl est remplacé par la chaîne d’environnement appropriée.

   * **E-mail de notification :** entrez votre adresse e-mail et cochez la case « Envoyer par e-mail en cas de défaillance ».

   * Cliquez sur le bouton **Tester la connexion**. Si le test de connexion réussit, cliquez sur le bouton **Enregistrer** en haut. En cas d’échec, vérifiez que l’URL et les informations d’identification Workday sont valides dans Workday.


### <a name="part-2-configure-attribute-mappings"></a>Partie 2 : configuration des mappages d’attributs 


Dans cette section, vous allez configurer le flux des données de l’utilisateur de Workday vers Active Directory.

1.  Dans l’onglet Approvisionnement sous **Mappages**, cliquez sur **Synchroniser les utilisateurs Azure AD sur Workday**.

2.  Dans le champ **Portée d’objet source**, vous pouvez éventuellement filtrer les ensembles d’utilisateurs dans Azure Active Directory dont les adresses e-mail sont différées dans Workday. La portée par défaut est « tous les utilisateurs dans Azure AD ». 

3.  Dans la section **Mappages d’attributs**, vous pouvez définir comment les attributs Workday sont mappés aux attributs Active Directory. Il existe un mappage pour l’adresse e-mail par défaut. Toutefois, l’ID correspondant doit être mis à jour pour faire correspondre les utilisateurs dans Azure AD et leurs entrées correspondantes dans Workday. Une méthode populaire de correspondance consiste à synchroniser l’ID de l’employé Workday avec extensionAttribute1-15 dans Azure AD puis, à utiliser cet attribut dans Azure AD pour faire à nouveau correspondre les utilisateurs dans Workday.

4.  Pour enregistrer vos mappages, cliquez sur **Enregistrer** en haut de la section Mappage d’attributs.

### <a name="part-3-start-the-service"></a>Partie 3 : démarrer le service
Une fois les parties 1 à 2 terminées, vous pouvez démarrer le service d’approvisionnement.

1.  Dans l’onglet **Approvisionnement**, définissez **État d’approvisionnement** sur **Activé**.

2. Cliquez sur **Enregistrer**.

3. Ceci démarre la synchronisation initiale, qui peut prendre un nombre variable d’heures en fonction du nombre d’utilisateurs dans Workday.

4. Les événements de synchronisation individuels peuvent être affichés dans l’onglet **Journaux d’audit**. **[Consultez le guide de création de rapports d’approvisionnement pour obtenir des instructions détaillées sur la façon de lire les journaux d’audit](active-directory-saas-provisioning-reporting.md)**

5. Une fois cette opération terminée, un rapport de synthèse de l’audit est écrit dans l’onglet **Approvisionnement**, comme indiqué ci-dessous.


## <a name="customizing-the-list-of-workday-user-attributes"></a>Personnaliser la liste des attributs d’utilisateurs Workday
Les applications d’attribution Workday pour Active Directory et Azure AD contiennent tous les deux une liste par défaut des attributs d’utilisateurs Workday utilisables. Toutefois, ces listes ne sont pas complètes. Workday prend en charge plusieurs centaines d’attributs d’utilisateurs possibles, qui peuvent être standard ou propres au locataire Workday concerné. 

Le service d’attribution Azure AD prend en charge la possibilité de personnaliser la liste ou l’attribut Workday de façon à inclure tous les attributs exposés dans l’opération [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) de l’API Ressources humaines.

Il vous faut pour cela utiliser [Workday Studio](https://community.workday.com/studio-download) afin d’extraire les expressions XPath qui représentent les attributs à utiliser, puis les ajouter à votre configuration d’attribution à l’aide de l’éditeur d’attributs avancé du Portail Azure.

**Pour récupérer une expression XPath pour un attribut d’utilisateur Workday :**

1. Téléchargez et installez [Workday Studio](https://community.workday.com/studio-download). Vous aurez besoin d’un compte Communauté Workday pour accéder au programme d’installation.

2. Téléchargez le fichier WDSL Human_Resources Workday à cette URL : https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Lancez Workday Studio.

4. Dans la barre de commandes, sélectionnez l’option **Workday > Tester le service web dans le testeur**.

5. Sélectionnez **Externe**, puis le fichier WSDL Human_Resources que vous avez téléchargé à l’étape 2.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio1.PNG)

6. Définissez le champ **Emplacement** sur `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, mais en remplaçant « IMPL-CC » par le type réel de votre instance, et « TENANT » par le nom réel de votre locataire.

7. Définissez **Opération** sur **Get_Workers**.

8.  Cliquez sur le petit lien **Configurer** sous les volets Requête/Réponse pour définir vos informations d’identification Workday. Cochez **Authentification**, puis entrez le nom d’utilisateur et le mot de passe de votre compte système d’intégration Workday. Veillez à mettre le nom d’utilisateur au format name@tenant et à laisser l’option **WS-Security UsernameToken** sélectionnée.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio2.PNG)

9. Sélectionnez **OK**.

10. Dans le volet **Requête**, collez le code XML ci-dessous et définissez **Employee_ID** sur l’ID d’employé d’un utilisateur réel de votre locataire Workday. Sélectionnez un utilisateur dont l’attribut à extraire est rempli.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. Cliquez sur la flèche verte **Envoyer la requête** pour exécuter la commande. En cas de réussite, la réponse apparaîtra dans le volet **Réponse**. Vérifiez que la réponse comporte les données de l’ID d’utilisateur que vous avez entré et non une erreur.

12. En cas de réussite, copiez le code XML du volet **Réponse** et enregistrez-le dans un fichier XML.

13. Dans la barre de commandes de Workday Studio, sélectionnez **Fichier > Ouvrir un fichier...**  et ouvrez le fichier XML que vous venez d’enregistrer. Il s’ouvre dans l’éditeur XML de Workday Studio.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio3.PNG)

14. Dans l’arborescence des fichiers, accédez à **/env:Envelope > env:Body > wd:Get_Workers_Response > wd:Response_Data > wd:Worker** pour trouver les données de votre utilisateur. 

15. Sous **wd:Worker**, recherchez l’attribut à ajouter, puis sélectionnez-le.

16. Copiez l’expression XPath de l’attribut sélectionné hors du champ **Chemin d’accès du document**.

17. Supprimez le préfixe **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** dans l’expression copiée. 

18. Si le dernier élément de l’expression copiée est un nœud (exemple : « /wd:Birth_Date »), ajoutez **/text()** à la fin de l’expression. Ce n’est pas nécessaire si le dernier élément est un attribut (exemple : « /@wd:type »).

19. Le résultat doit se présenter ainsi : `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. C’est ce que vous allez copier sur le Portail Azure.


**Pour ajouter votre attribut d’utilisateur Workday personnalisé à votre configuration d’attribution :**

1. Lancez le [Portail Azure](https://portal.azure.com), accédez à la section Attribution de votre application d’attribution Workday, selon la procédure décrite plus haut dans ce didacticiel.

2. Définissez **État d’attribution** sur **Désactivé**, puis sélectionnez **Enregistrer**. Ainsi, les modifications ne prendront effet que lorsque vous le déciderez.

3. Sous **Mappages**, sélectionnez **Synchroniser les travailleurs en local** (ou **Synchroniser les travailleurs sur Azure AD**).

4. Faites défiler l’écran suivant jusqu’en bas, puis sélectionnez **Afficher les options avancées**.

5. Sélectionnez **Modifier la liste d’attributs de Workday**.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio_AAD1.PNG)

6. Faites défiler la liste d’attributs jusqu’en bas, où se trouvent les champs d’entrée.

7. Pour **Nom**, entrez le nom d’affichage de votre attribut.

8. Pour **Type**, sélectionnez le type correspondant à votre attribut (le plus courant est **Chaîne**).

9. Pour **Expression API**, entrez l’expression XPath que vous avez copiée dans Workday Studio. Exemple : `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Sélectionnez **Ajouter un attribut**.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio_AAD2.PNG)

11. Sélectionnez **Enregistrer** ci-dessus, puis **Oui** dans la boîte de dialogue. Fermez l’écran Mappage des attributs s’il est toujours ouvert.

12. Dans l’onglet **Attribution** principal, sélectionnez à nouveau **Synchroniser les travailleurs en local** (ou **Synchroniser les travailleurs sur Azure AD**).

13. Sélectionnez **Ajouter un mappage**.

14. Votre nouvel attribut devrait maintenant apparaître dans la liste **Attribut source**.

15. Ajoutez le mappage de votre choix pour votre nouvel attribut.

16. Ensuite, pensez à redéfinir **État d’attribution** sur **Activé** et à enregistrer.


## <a name="known-issues"></a>Problèmes connus

* Lorsque vous exécutez la commande PowerShell **Add-ADSyncAgentAzureActiveDirectoryConfiguration**, il existe actuellement un problème connu avec les informations d’identification d’administrateur global qui ne fonctionnent pas si elles utilisent un domaine personnalisé (exemple : admin@contoso.com). Pour résoudre ce problème, créez et utilisez un compte d’administrateur global dans Azure AD avec un domaine onmicrosoft.com (exemple : admin@contoso.onmicrosoft.com).

* Un problème précédent avec les journaux d’audit n’apparaissant pas dans les locataires Azure AD situés dans l’Union européenne a été résolu. Toutefois, une configuration supplémentaire de l’agent est requise pour les locataires Azure AD dans l’Union européenne. Pour plus d’informations, consultez [Partie 3 : configurer l’agent de synchronisation local](#Part 3: Configure the on-premises synchronization agent)


## <a name="additional-resources"></a>Ressources supplémentaires
* [Didacticiel : configurer l’authentification unique entre Workday et Azure Active Directory](active-directory-saas-workday-tutorial.md)
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting)
