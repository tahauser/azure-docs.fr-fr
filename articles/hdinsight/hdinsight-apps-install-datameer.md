---
title: Installer une application publiée - Datameer - Azure HDInsight | Microsoft Docs
description: Installez et utilisez l’application Hadoop tierce Datameer.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 8a898b4f82cf2d7e05e8c3895e5eddd8cf02b173
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---datameer"></a>Installer une application publiée - Datameer

Cet article explique comment installer et exécuter l’application Hadoop publiée [Datameer](https://www.datameer.com/) sur Azure HDInsight. Pour obtenir une vue d’ensemble de la plateforme d’application HDInsight et la liste des applications publiées d’éditeurs de logiciels indépendants (ISV), consultez [Installer des applications Hadoop tierces sur Azure HDInsight](hdinsight-apps-install-applications.md). Pour des instructions d’installation de votre propre application, consultez la page [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md).

## <a name="about-datameer"></a>À propos de Datameer

Datameer est une application native pour la plateforme Hadoop, qui étend les fonctionnalités existantes d’Azure HDInsight et fournit une intégration, une préparation et une analyse rapides des données structurées et non structurées. Datameer peut accéder à plus de 70 sources et formats : structurés, semi-structurés et non structurés. Vous pouvez directement charger des données, ou utiliser leurs liaisons de données uniques pour extraire des données à la demande. Les fonctionnalités en libre-service et l’interface de feuille de calcul familière de Datameer réduisent la complexité de la technologie Big Data et accélèrent l’exploitation des données. L’interface de feuille de calcul fournit un mécanisme simple pour la saisie de formules déclaratives qui sont ensuite traduites en travaux Hadoop optimisés. Avec Datameer et vos compétences relatives à Excel et à l’analyse décisionnelle, vous pouvez utiliser Hadoop dans le cloud rapidement. Pour plus d’informations, consultez la [documentation de Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>Prérequis

Pour installer cette application sur un cluster existant ou sur un nouveau cluster HDInsight, la configuration suivante est nécessaire :

* Niveau cluster : standard
* Type de cluster : Hadoop
* Version de cluster : 3.4

## <a name="install-the-datameer-published-application"></a>Installer l’application publiée Datameer

Pour obtenir des instructions détaillées sur l’installation de cette application et d’autres applications ISV disponibles, consultez [Installer des applications Hadoop tierces sur Azure HDInsight](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Lancer Datameer

1. Après l’installation, vous pouvez lancer Datameer à partir de votre cluster dans le portail Azure en accédant au volet **Paramètres**, puis en cliquant sur **Applications** sous la catégorie **Général**. Le volet Applications installées répertorie les applications installées.

    ![Application Datameer installée](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. Quand vous sélectionnez Datameer, un lien vers la page web apparaît, ainsi que le chemin du point de terminaison SSH. Sélectionnez le lien vers la PAGE WEB.

3. Au premier lancement, il existe deux options de licence : soit un essai gratuit de 14 jours, soit l’activation d’une licence existante.

    ![Options de licence](./media/hdinsight-apps-install-datameer/license.png)

4. Après avoir suivi les instructions de votre option de licence, un formulaire de connexion s’affiche. Entrez les informations d’identification par défaut affichées avant le formulaire de connexion. Une fois connecté, acceptez le contrat de logiciel pour continuer.

    ![Connexion](./media/hdinsight-apps-install-datameer/login.png)

Les étapes suivantes présentent une démonstration « Hello World ».

1. [Téléchargez l’exemple de fichier CSV](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Cliquez sur le signe **+** en haut du tableau de bord Datameer, puis cliquez sur **Chargement de fichiers**.

    ![Chargement de fichiers](./media/hdinsight-apps-install-datameer/upload.png)

3. Dans la boîte de dialogue de chargement, recherchez et sélectionnez le fichier **Hello World.csv** téléchargé. Assurez-vous que **Type de fichier** est défini sur CSV/TSV. Sélectionnez **Suivant**. Continuez à cliquer sur **Suivant** jusqu’à ce que vous atteigniez la fin de l’Assistant.

    ![Chargement de fichiers](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. Nommez le fichier **Hello World** sous un nouveau dossier. Renommez le nouveau dossier « Démo ». Sélectionnez **Enregistrer**.

    ![Enregistrer](./media/hdinsight-apps-install-datameer/save.png)

5. Cliquez sur le signe **+** une fois de plus et sélectionnez **Classeur** pour créer un classeur pour les données.

    ![Ajouter un classeur](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Développez le dossier **Données**, **FileUploads**, puis le dossier **Démo**que vous avez créé lors de l’enregistrement du fichier « Hello World ». Sélectionnez **Hello World** dans la liste de fichiers, puis cliquez sur **Ajouter des données**.

    ![Enregistrer](./media/hdinsight-apps-install-datameer/select-file.png)

7. Vous voyez que les données sont chargées dans une interface de feuille de calcul. Pour sélectionner un sous-ensemble de données, sélectionnez le bouton **Filtrer** dans la barre d’outils.

    ![Bouton Filtrer](./media/hdinsight-apps-install-datameer/filter-button.png)

8. Dans la boîte de dialogue Appliquer le filtre, sélectionnez la colonne **Ville**, l’opérateur **est égal à** et tapez **Chicago** dans la zone de texte du filtre. Cochez la case **Create filter in new sheet** (Créer un filtre dans une nouvelle feuille), puis sélectionnez **Créer un filtre**.

    ![Appliquer le filtre](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Enregistrez le classeur en cliquant sur **Fichier**, puis **Enregistrer**. Fournissez un nom, par exemple « Classeur Hello World ».

10. Les options relatives à la méthode d’exécution et au moment de l’exécution du classeur vous sont alors présentées. Pour l’instant, laissez toutes les options sur leurs valeurs par défaut, puis cochez la case **Start calculation process immediately after save** (Démarrer le processus de calcul immédiatement après l’enregistrement), puis sélectionnez **Enregistrer**.

    ![Enregistrer le classeur](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer fournit des outils de visualisation puissants. Pour afficher les données, créez une infographie. Sélectionnez le signe **+** en haut du tableau de bord, puis sélectionnez **Infographie**.

    ![Ajouter une infographie](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Faites glisser un widget de graphique à barres dans la liste de widgets sur la gauche, comme indiqué à l’étape 1 du schéma suivant. Ensuite, parcourez le dossier Données sous le navigateur de données sur la droite, développez votre classeur, puis la feuille de calcul que vous avez ajoutée avec le filtre (étape 2). Faites glisser la colonne **Nom** en haut du graphique à barres et déposez-la dans la cible **Étiquette** pour définir la colonne de nom du classeur en tant que champ d’étiquette du graphique (étape 3).

    ![Infographie](./media/hdinsight-apps-install-datameer/infographic.png)

13. Pour définir l’âge en tant qu’axe des Y du graphique, faites glisser la colonne **Âge** dans le champ **Données** du graphique.

    ![Infographie](./media/hdinsight-apps-install-datameer/infographic-age.png)

Félicitations ! Vous avez créé une visualisation de vos données sans écrire de code. Vous pouvez maintenant explorer les variations et des visualisations supplémentaires.

## <a name="next-steps"></a>étapes suivantes

* [Documentation Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Installer des applications personnalisées Hadoop sur Azure HDInsight](hdinsight-apps-install-custom-applications.md) : découvrez comment déployer des applications HDInsight non publiées vers HDInsight.
* [Publier des applications HDInsight dans la Place de marché Azure](hdinsight-apps-publish-applications.md): découvrez comment publier vos applications HDInsight personnalisées sur la Place de marché Azure.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN : installer une application HDInsight) : découvrez comment définir des applications HDInsight.
* [Personnaliser des clusters HDInsight Linux à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md) : apprenez à utiliser l’action de script pour installer des applications supplémentaires.
* [Utiliser les nœuds de périphérie vides sur les clusters Hadoop dans HDInsight](hdinsight-apps-use-edge-node.md) : apprenez à utiliser un nœud de périphérie vide pour accéder aux clusters HDInsight, ainsi que pour tester et héberger des applications HDInsight.
