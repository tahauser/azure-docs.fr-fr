---
title: "Azure HDInsight Tools - Définir l’environnement interactif de PySpark pour Visual Studio Code | Microsoft Docs"
description: "Découvrez comment utiliser Azure HDInsight Tools pour Visual Studio Code pour créer et envoyer des requêtes et des scripts."
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: 
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 5a64023df813262c461b9d772b722ebd613369ed
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Définir l’environnement interactif de PySpark pour Visual Studio Code

Les étapes suivantes vous montrent comment installer des packages Python par l’exécution de **HDInsight: PySpark Interactive**.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>Définir l’environnement interactif de PySpark sur MacOS et Linux
Si vous utilisez **Python 3.x**, vous devez utiliser la commande **pip3** pour les étapes suivantes :

1. Assurez-vous de l’installation de **Python** et de **pip**.
 
    ![Version de Python et de pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Installez Jupyter.
    ```
    sudo pip install jupyter
    ```
   Le message d’erreur suivant peut s’afficher sur Linux et MacOS :

   ![Erreur 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Installez **libkrb5-dev** (pour Linux). Le message d’erreur suivant peut s’afficher :

   ![Erreur 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. Installez **sparkmagic**.
   ```
   sudo pip install sparkmagic
   ```

4. Vérifiez que **ipywidgets** est correctement installé en exécutant :
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Installer les noyaux wrapper](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Installez les noyaux wrapper. Exécutez **pip show sparkmagic**. La sortie indique le chemin d’accès pour l’installation de **sparkmagic**. 

    ![Emplacement de sparkmagic](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Accédez à l’emplacement puis exécutez :

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![Installation de jupyter kernelspec](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Vérifiez l’état de l’installation.

    ```
    jupyter-kernelspec list
    ```
    ![liste de jupyter kernelspec](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Pour les noyaux disponibles : 
    - **python2** et **pysparkkernel** correspondent à **python 2.x**. 
    - **python3** et **pyspark3kernel** correspondent à **python 3.x**. 

8. Redémarrez VS Code puis revenez à l’éditeur de script qui exécute **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Étapes suivantes

### <a name="demo"></a>Démonstration
* HDInsight pour VS Code : [vidéo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser Azure HDInsight Tools pour Visual Studio Code](hdinsight-for-vscode.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour créer et soumettre des applications Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualiser des données Hive à l’aide de Microsoft Power BI dans Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Utiliser Zeppelin pour exécuter des requêtes Hive dans Azure HDInsight](hdinsight-connect-hive-zeppelin.md)
