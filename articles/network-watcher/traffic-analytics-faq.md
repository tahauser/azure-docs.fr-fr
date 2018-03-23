---
title: Forum aux questions pour Azure Traffic Analytics | Microsoft Docs
description: Découvrez les réponses aux questions les plus fréquemment posées sur Traffic Analytics.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: fd97e0ca7615691c537dcb1dc18643627046742d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Forum aux questions pour Traffic Analytics

1.  Quels sont les prérequis pour utiliser Traffic Analytics ?

    Traffic Analytics nécessite les prérequis suivants :

    - Un abonnement actif à Network Watcher
    - Des journaux de flux de groupe de sécurité réseau activés pour les groupes de sécurité réseau à surveiller
    - Un compte de stockage Azure pour stocker les journaux de flux bruts
    - Un espace de travail Log Analytics (OMS), avec accès en lecture et écriture

2.  Dans quelles régions Azure Traffic Analytics est-il disponible ?

    Dans la préversion, vous pouvez utiliser Traffic Analytics pour les groupes de sécurité réseau dans les **régions prises en charge** suivantes : États-Unis Centre-Ouest, Est des États-Unis, Est des États-Unis 2, Nord-Centre des États-Unis, Sud-Centre des États-Unis, Centre des États-Unis, Ouest des États-Unis, Ouest des États-Unis 2, Europe de l’Ouest, Europe du Nord, Royaume-Uni Ouest, Royaume-Uni Sud, Est de l’Australie et Sud-Est de l’Australie. L’espace de travail Log Analytics doit se trouver dans la région États-Unis Centre-Ouest, Est des États-Unis, Europe de l’Ouest, Sud-Est de l’Australie ou Royaume-Uni Sud.

3.  Puis-je activer des journaux de flux pour des groupes de sécurité réseau qui se trouvent dans des régions différentes de mon espace de travail OMS ?

    OUI

4.  Est-il possible de configurer plusieurs groupes de sécurité réseau dans un seul espace de travail ?

    OUI

5.  Puis-je utiliser un espace de travail OMS existant ?

    Oui, si vous sélectionnez un espace de travail existant, assurez-vous qu’il a été migré vers le nouveau langage de requête. Si vous ne souhaitez pas mettre à niveau l’espace de travail, vous devez en créer un autre. Pour plus d’informations sur le nouveau langage de requête, consultez [Mise à niveau Azure Log Analytics avec la nouvelle recherche dans les journaux](../log-analytics/log-analytics-log-search-upgrade.md).

6.  Mon compte de stockage Azure peut-il être associé à un abonnement et mon espace de travail OMS à un autre abonnement ?

    OUI

7.  Puis-je stocker des journaux bruts dans un compte de stockage différent dans un autre abonnement ?

    Non. Vous pouvez stocker des journaux bruts dans n’importe quel compte de stockage où un groupe de sécurité réseau est activé pour les journaux de flux. Toutefois, le compte de stockage et les journaux bruts doivent être dans le même abonnement et la même région.

8.  Si je reçois une erreur « Introuvable » lors de la configuration d’un groupe de sécurité réseau pour Traffic Analytics, comment puis-je y remédier ?

    Sélectionnez une des régions prises en charge répertoriées dans la question 2. Si vous sélectionnez une région non prise en charge, vous recevez une erreur « Introuvable ».

9.  Sous les journaux de flux du groupe de sécurité réseau, j’obtiens le statut « Échec de chargement ». Que dois-je faire ?

    Pour que la journalisation du flux fonctionne correctement, le fournisseur Microsoft.Insights doit être inscrit. Si vous n’êtes pas certain que le fournisseur Microsoft.Insights est inscrit pour votre abonnement, remplacez *xxxxx-xxxxx-xxxxxx-xxxx* dans la commande suivante, puis exécutez les commandes ci-dessous à partir de PowerShell :

    ```powershell-interactive
    **Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
    **Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
    ```

10. J’ai configuré la solution. Pourquoi ne vois-je rien sur le tableau de bord ?

    L’affichage du tableau de bord peut prendre jusqu’à 30 minutes la première fois. La solution doit tout d’abord agréger suffisamment de données pour en dériver des informations utiles, avant la génération des rapports. 

11.  Le message suivant s’affiche : « Impossible de trouver des données dans cet espace de travail pour l’intervalle de temps sélectionné. Essayez de modifier l’intervalle ou sélectionnez un autre espace de travail ». Comment résoudre le problème ?

        Tentez les opérations suivantes :
        - Essayez de modifier l’intervalle de temps dans la barre supérieure.
        - Sélectionnez un autre espace de travail Log Analytics dans la barre supérieure.
        - Essayez d’accéder à Traffic Analytics après 30 minutes, s’il a été récemment activé.
    
        Si les problèmes persistent, expliquez votre problème dans le [forum des utilisateurs](https://feedback.azure.com/forums/217313-networking?category_id=195844).

12.  Le message suivant s’affiche : « 1) Première analyse des journaux de flux de votre groupe de sécurité réseau. Ce processus peut prendre de 20 à 30 minutes. Vérifiez ultérieurement. (2) Si l’étape ci-dessus ne fonctionne pas et que votre espace de travail se trouve sous la référence SKU libre, vérifiez l’utilisation de votre espace de travail pour valider le quota. Sinon, reportez-vous à la FAQ pour plus d’informations. » Comment résoudre le problème ?

        Vous pouvez recevoir cette erreur pour les raisons suivantes :
        - Traffic Analytics a peut-être été récemment activé et l’agrégation de données est en cours pour pouvoir tirer des informations utiles avant de générer des rapports. Dans ce cas, essayez à nouveau après 30 minutes.
        - Votre espace de travail OMS est sous la référence SKU libre et il enfreint les limites de quota. Dans ce cas, vous devrez peut-être utiliser un espace de travail dans une référence SKU de capacité supérieure.
    
        Si les problèmes persistent, expliquez votre problème dans le [forum des utilisateurs](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
13.  Le message suivant s’affiche : « Il semble qu’il existe des données de ressources (topologie), mais aucune information de flux. Cliquez ici pour afficher les données de ressources et reportez-vous à la FAQ pour plus d’informations. ». Comment résoudre le problème ?

        Vous voyez les informations de ressources sur le tableau de bord. Toutefois, aucune statistique de flux n’est présente. Les données sont peut-être manquantes en l’absence de flux de communication entre les ressources. Attendez 60 minutes et revérifier l’état. Si vous êtes sûr que les ressources communiquent, expliquez votre problème dans le [forum des utilisateurs](https://feedback.azure.com/forums/217313-networking?category_id=195844).

14.  Quel est le prix de Traffic Analytics ?

        Aucun frais n’est facturé pour la préversion publique de Traffic Analytics. La génération de journaux de flux de groupe de sécurité réseau et la conservation des données dans un espace de travail OMS sont soumis à des frais selon les tarifs publiés.

15.  Comment puis-je naviguer dans la vue de la carte géographique à l’aide du clavier ?

        La page de la carte géographique contient deux sections principales :
    
        - **Bannière** : la bannière placée en haut de la carte géographique permet de sélectionner les filtres de distribution de trafic via des boutons comme Déploiement/Non-déploiement/Actif/Inactif/Traffic Analytics activé/Traffic Analytics non activé/Trafic des pays/Inoffensif/Malveillant/Trafic malveillant autorisé et les informations de la légende. Lorsque vous cliquez sur certains boutons, le filtre correspondant est appliqué à la carte. Par exemple, si un utilisateur clique sur le bouton de filtre « Actif » sous la bannière, la carte met en évidence les centres de données actifs dans votre déploiement.
        - **Carte** : la section de la carte placée sous la bannière montre la distribution du trafic dans les centres de données Azure et les pays.
    
        **Navigation au clavier sur la bannière**
    
        - Par défaut, la sélection dans la bannière de la page de carte géographique est le bouton de filtre « Contrôleurs de domaine Azure ».
        - Pour accéder à un autre bouton de filtre, vous pouvez utiliser la touche `Tab` ou `Right arrow` pour vous déplacer vers le bas. Pour revenir en l’arrière, utilisez la touche `Shift+Tab` ou `Left arrow`. La priorité de direction de navigation vers l’avant va de gauche à droite, puis de haut en bas.
        - Appuyez sur la touche de direction `Enter` ou `Down` pour appliquer le filtre sélectionné. Selon la sélection de filtre et le déploiement, un ou plusieurs nœuds sous la section de la carte sont mis en surbrillance.
            - Pour basculer entre la **bannière** et la **carte**, appuyez sur `Ctrl+F6`.
        
        **Navigation au clavier sur la carte**
    
        - Une fois que vous avez sélectionné un filtre sur la bannière et appuyé sur `Ctrl+F6`, le focus se déplace vers l’un des nœuds en surbrillance (**centre de données Azure** ou **pays/région**) dans la vue cartographique.
        - Pour accéder à d’autres nœuds en surbrillance sur la carte, vous pouvez utiliser la touche `Tab` ou `Right arrow` pour les déplacements vers l’avant, et la touche `Shift+Tab` ou `Left arrow` pour les déplacements vers l’arrière.
        - Pour sélectionner n’importe quel nœud en surbrillance sur la carte, utilisez la touche `Enter` ou `Down arrow`.
        - Lorsque vous sélectionnez ces nœuds, le focus se déplace vers la **boîte à outils Informations** du nœud. Par défaut, le focus se déplace sur le bouton Fermer de la **boîte à outils Informations**. Pour naviguer dans la vue de la **boîte**, utilisez les touches `Right` et `Left arrow` pour vous déplacer vers l’avant et vers l’arrière, respectivement. Appuyer sur `Enter` a le même effet que cliquer sur le bouton actif dans la **boîte à outils Informations**.
        - Si vous appuyez sur `Tab` tandis que le focus est sur la **boîte à outils Informations**, le focus se déplace vers les points de terminaison du même continent que le nœud sélectionné. Vous pouvez utiliser les touches `Right` et `Left arrow` pour parcourir ces points de terminaison.
        - Pour accéder à un autre cluster de continents/points de terminaison de flux, utilisez `Tab` pour les déplacements vers l’avant et `Shift+Tab` pour les déplacements vers l’arrière.
        - Une fois que le focus est sur `Continent clusters`, utilisez la touche de direction `Enter` ou `Down` pour mettre en surbrillance les points de terminaison à l’intérieur du cluster de continents. Pour parcourir les points de terminaison et accéder au bouton Fermer de la boîte d’informations du cluster de continents, utilisez les touches `Right` ou `Left arrow` pour les déplacements vers l’avant et vers l’arrière, respectivement. Sur n’importe quel point de terminaison, vous pouvez utiliser `Shift+L` pour basculer vers la ligne de connexion à partir du nœud sélectionné pour le point de terminaison. Appuyez sur `Shift+L` à nouveau pour atteindre le point de terminaison sélectionné.
        
        À tout moment :
    
        - `ESC` réduit la sélection développée.
        - La touche `UP Arrow` effectue la même action que `ESC`. La touche `Down arrow` effectue la même action que `Enter`.
        - Utilisez `Shift+Plus` pour effectuer un zoom avant et `Shift+Minus` pour effectuer un zoom arrière.