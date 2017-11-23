---
title: "Guide pour un exemple d’application mutualisée SQL Database - SaaS Wingtip | Microsoft Docs"
description: "Fournit des instructions et des étapes pour installer et exécuter l’exemple d’application mutualisée qui utilise la base de données SQL Azure, l’exemple Wingtip SaaS."
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: genemi
ms.openlocfilehash: fbfaea938676991cf6280e5dd8c1e1190aa268a8
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="guidance-and-tips-for-azure-sql-database-multi-tenant-saas-app-example"></a>Instructions et conseils pour l’exemple d’application de base de données SQL Azure mutualisée SaaS


## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Télécharger et débloquer les scripts Wingtip SaaS

Le contenu exécutable (scripts, DLL) peut être bloqué par Windows lorsque des fichiers zip sont téléchargés à partir d’une source externe puis extraits. Lorsque vous extrayez les scripts d’un fichier zip, ***suivez les étapes ci-dessous pour débloquer le fichier .zip avant l’extraction***. Cela garantit que les scripts sont autorisés à s’exécuter.

1. Accédez au [référentiel github Wingtip SaaS](https://github.com/Microsoft/WingtipSaaS).
2. Cliquez sur **Cloner ou télécharger**.
3. Cliquez sur **Télécharger ZIP** et enregistrez le fichier.
4. Cliquez avec le bouton droit sur le fichier **WingtipSaaS-master.zip**, puis sélectionnez **Propriétés**.
5. Sous l’onglet **Général**, sélectionnez **Débloquer**.
6. Cliquez sur **OK**.
7. Procédez à l’extraction des fichiers.

Les scripts se trouvent dans le dossier *..\\WingtipSaaS-master\\Learning Modules*.


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>Utilisation des scripts PowerShell de SaaS Wingtip

Pour tirer le meilleur parti de l’exemple, vous devez approfondir les scripts fournis. Utilisez des points d’arrêt et parcourez les scripts en examinant les détails de l’implémentation des différents motifs SaaS. Pour parcourir facilement les scripts et les modules fournis afin d’acquérir une compréhension optimale de ceux-ci, nous vous recommandons d’utiliser [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Mettre à jour le fichier de configuration pour votre déploiement

Modifiez le fichier **UserConfig.psm1** avec le groupe de ressources et la valeur utilisateur que vous avez définis durant le déploiement :

1. Ouvrez le *PowerShell ISE* puis chargez...\\Learning Modules\\*UserConfig.psm1*. 
2. Mettez à jour *ResourceGroupName* et *Name* avec les valeurs spécifique de votre déploiement (sur les lignes 10 et 11 uniquement).
3. N’oubliez pas d’enregistrer les modifications.

La définition de ces valeurs ici vous évite simplement d’avoir à mettre à jour ces valeurs spécifiques du déploiement dans chaque script.

### <a name="execute-scripts-by-pressing-f5"></a>Exécuter des scripts en appuyant sur F5

Plusieurs scripts utilisent *$PSScriptRoot* pour parcourir les dossiers, et *$PSScriptRoot* est évalué uniquement lors de l’exécution de scripts en appuyant sur **F5**.  La mise en surbrillance et l’exécution d’une sélection (**F8**) pouvant entraîner des erreurs, nous vous conseillons d’appuyer sur **F5** lors de l’exécution des scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Parcourir les scripts pour examiner l’implémentation

La meilleure façon de comprendre les scripts consiste à les parcourir pour voir ce qu’ils font. Extrayez les scripts **Demo-** inclus, qui présentent un flux de travail de haut niveau facile pour suivre. Les scripts **Demo-** présentent les étapes nécessaires à l’accomplissement de chaque tâche. Par conséquent, définissez des points d’arrêt, puis approfondissez les appels individuels pour voir les détails d’implémentation des différents modèles SaaS.

Conseils pour l’exploration et le parcours des scripts PowerShell :

- Ouvrez les scripts **Demo-** dans le PowerShell ISE.
- Exécutez ou continuez à exécuter le script à l’aide de la touche **F5** (l’utilisation de la touche **F8** n’est pas conseillée, car *$PSScriptRoot* n’est pas évalué lors de l’exécution des sélections d’un script).
- Placez des points d’arrêt en cliquant ou en sélectionnant une ligne et en appuyant sur **F9**.
- Survolez l’appel d’une fonction ou d’un script à l’aide de la touche **F10**.
- Parcourez l’appel d’une fonction ou d’un script à l’aide de la touche **F11**.
- Sortez de l’appel actuel d’une fonction ou d’un script en appuyant sur **MAJ + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorez le schéma de base de données et exécutez des requêtes SQL à l’aide de SSMS

Utilisez [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) pour vous connecter et parcourir les bases de données et les serveurs de l’application.

Le déploiement comprend initialement deux serveurs de base de données SQL auxquels se connecter : le serveur *tenants1-&lt;Utilisateur&gt;* et le serveur *catalog-&lt;Utilisateur&gt;*. Pour garantir une connexion de démonstration réussie, les deux serveurs ont un [règle de pare-feu](sql-database-firewall-configure.md) autorisant toutes les adresses IP.


1. Ouvrez *SSMS* et connectez-vous au serveur *tenants1-&lt;User&gt;.database.windows.net*.
2. Cliquez sur **Connexion** > **Moteur de base de données...** :

   ![catalog server](media/saas-dbpertenant-wingtip-app-guidance-tips/connect.png)

3. Les informations d’identification de la démonstration sont : nom d’utilisateur = *developer*, mot de passe = *P@ssword1*.

   ![connection](media/saas-dbpertenant-wingtip-app-guidance-tips/tenants1-connect.png)

4. Répétez les étapes 2 et 3 et connectez-vous au serveur *catalog-&lt;User&gt;.database.windows.net*.


Une fois la connexion établie, vous devez voir les deux serveurs. Votre liste des bases de données peut être différente, selon les clients que vous avez approvisionnés.

![object explorer](media/saas-dbpertenant-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Étapes suivantes

[Déployer l’application SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)

