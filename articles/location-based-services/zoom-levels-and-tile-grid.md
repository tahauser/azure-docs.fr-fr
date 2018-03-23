---
title: Niveaux de zoom et grille mosaïque dans Azure Location Based Services | Microsoft Docs
description: Découvrez les niveaux de zoom et la grille mosaïque dans Azure Location Based Services
services: location-based-services
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 3/6/2018
ms.topic: article
ms.service: location-based-services
documentationcenter: ''
manager: cpendle
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3a86bf84ad31933cc5008591a275d4f4aa52c9f1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="zoom-levels-and-tile-grid"></a>Niveaux de zoom et grille mosaïque
Azure Location Based Services utilise le système de coordonnées de projection Spherical Mercator (EPSG : 3857).

Le monde est divisé en mosaïques carrées. L’affichage en trames comporte 19 niveaux de zoom, numérotés de 0 à 18. L’affichage en vecteurs comporte 21 niveaux de zoom, numérotés de 0 à 20. Au niveau de zoom 0, le monde entier tient dans une seule mosaïque :

![Mosaïque du monde](./media/zoom-levels-and-tile-grid/world0.png)

Le niveau de zoom 1 utilise 4 mosaïques pour afficher le monde : un carré de 2 x 2

![Mosaïque du monde en haut à gauche](./media/zoom-levels-and-tile-grid/world1a.png)     ![Mosaïque du monde en haut à droite](./media/zoom-levels-and-tile-grid/world1c.png) 

![Mosaïque du monde en bas à gauche](./media/zoom-levels-and-tile-grid/world1b.png)     ![Mosaïque du monde en bas à droite](./media/zoom-levels-and-tile-grid/world1d.png) 


Chaque niveau de zoom suivant divise en quatre les mosaïques du précédent, en créant une grille de 2<sup>zooms</sup> x 2<sup>zooms</sup>. Le niveau de zoom 20 est une grille de 2<sup>20</sup> x 2<sup>20</sup>, ou de 1 048 576 x 1 048 576 mosaïques (109 951 162 778 au total).

Le tableau complet de valeurs pour les niveaux de zoom est proposé ici :

|niveau de zoom|compteurs/pixel|compteurs/côté mosaïque|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5.|4892|1252352|
|6.|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9.|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13.|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0,6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|

Les mosaïques sont appelées selon le niveau de zoom et les coordonnées x et y correspondant à la position de la mosaïque sur la grille pour ce niveau de zoom.

Lorsque vous déterminez le niveau de zoom à utiliser, n’oubliez pas que chaque emplacement correspond à une position fixe sur sa mosaïque. Cela signifie que le nombre de mosaïques nécessaires pour afficher l’étendue donnée d’un territoire dépend du placement spécifique de la grille de zoom dans le monde. Par exemple, si deux points sont éloignés l’un de l’autre de 900 mètres, trois mosaïques *peuvent* suffire pour afficher un itinéraire entre eux au niveau de zoom 17. Toutefois, si le point occidental se trouve à droite de sa mosaïque et que le point oriental se trouve à gauche, quatre vignettes peuvent être utilisées :

![Échelle de démonstration du zoom](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Lorsque le niveau de zoom est déterminé, les valeurs x et y peuvent être calculées. La mosaïque en haut à gauche de chaque grille de zoom correspond à x = 0, y = 0 ; la mosaïque en bas à droite correspond à x=2<sup>zoom -1</sup>, y=2<sup>zoom-1</sup>.

La grille de zoom pour le niveau de zoom 1 est proposée ici :

![Grille de zoom pour le niveau de zoom 1](./media/zoom-levels-and-tile-grid/api_x_y.png)
