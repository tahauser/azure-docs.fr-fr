
Les tailles de machine virtuelle optimisées pour le GPU sont destinées aux machines virtuelles spécialisées conçues pour les opérations graphiques lourdes et la retouche vidéo. Disponible avec un ou plusieurs GPU. Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur les performances de réseau et de débit de stockage pour chaque taille de ce regroupement. 


Les tailles NC et NV sont également appelées instances compatibles GPU. Il s’agit de tailles de machine virtuelle spécialisée qui incluent des cartes GPU NVIDIA, optimisées pour différents scénarios et cas d’utilisation. Les tailles NV sont optimisées et conçues pour la visualisation à distance, la diffusion en continu, les jeux, le codage et les scénarios VDI utilisant des infrastructures comme OpenGL et DirectX. Les tailles NC sont optimisées pour les applications nécessitant des ressources réseau et de calcul importantes, les algorithmes, notamment les applications et simulations CUDA et OpenCL. 


Les instances NV sont alimentées par une carte GPU NVIDIA Tesla M60 et NVIDIA GRID pour les applications de bureautique accélérées et les bureaux virtuels où les clients peuvent visualiser leurs données ou simulations. Les utilisateurs peuvent visualiser leurs flux de travail nécessitant beaucoup de graphismes sur les instances NV afin d’obtenir des fonctionnalités graphiques de qualité supérieure et exécuter par ailleurs des charges de travail simple précision comme le codage et le rendu. Le modèle Tesla M60 offre 4 096 cœurs CUDA dans une conception à double GPU avec jusqu'à 36 flux H.264 1080p. 

Les instances NC sont alimentées par une carte NVIDIA Tesla K80. Les utilisateurs peuvent désormais exploiter plus rapidement leurs données en tirant parti de CUDA pour les applications d’exploration d’énergie, de simulations de crash, de rendu avec lancer de rayon, de formation approfondie et bien plus encore. Le module Tesla K80 offre 4 992 cœurs CUDA avec une conception à double GPU, jusqu'à 2,91 téraflops double précision et 8,93 téraflops simple précision.

## <a name="nv-instances"></a>Instances NV

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Disques de données max. |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1 440 | 4 | 64 |

1 GPU = une moitié de carte M60.

## <a name="nc-instances"></a>Instances NC

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Disques de données max. |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1 440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1 440 | 4 | 64 |

1 GPU = une moitié de carte K80.

*Prenant en charge RDMA


