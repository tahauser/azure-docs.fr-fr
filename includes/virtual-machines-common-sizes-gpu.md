
Les tailles de machine virtuelle au GPU optimisé sont des machines virtuelles spécialisées disponibles avec des GPU NVIDIA uniques ou multiples. Ces tailles sont conçues pour des charges de travail de visualisation, mais également de calcul et d’affichage graphique intensifs. Cet article fournit des informations sur le nombre et le type de GPU, de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur la bande passante réseau et le débit de stockage pour chaque taille de ce regroupement. 

* Les tailles des cœurs **NC, NCv2 et ND** sont optimisées pour les applications nécessitant des ressources réseau et de calcul importantes, les algorithmes, notamment les applications et simulations CUDA et OpenCL, l’intelligence artificielle et le Deep Learning. 
* Les tailles des cœurs **NV** sont optimisées et conçues pour la visualisation à distance, la diffusion en continu, les jeux, le codage et les scénarios VDI utilisant des infrastructures comme OpenGL et DirectX.  


## <a name="nc-instances"></a>Instances NC

Les instances NC sont optimisées par la carte [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf). Les utilisateurs peuvent exploiter plus rapidement leurs données en tirant parti de CUDA pour les applications d’exploration d’énergie, de simulations de crash, de rendu avec lancer de rayon, de formation approfondie et bien plus encore. La configuration NC24r fournit une interface réseau à haut débit et à faible latence optimisée pour les charges de travail d’informatique parallèle fortement couplées.


| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Disques de données max. |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6. |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1 440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1 440 | 4 | 64 |

1 GPU = une moitié de carte K80.

*Prenant en charge RDMA

## <a name="ncv2-instances"></a>Instances NCv2

Les instances NCv2 représentent la nouvelle génération de machines de série NC, optimisées par les GPU [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf). Ces GPU peuvent fournir des performances de calcul deux fois supérieures à celles de la série NC actuelle. Les clients peuvent tirer parti de ces GPU mis à jour pour les charges de travail HPC traditionnelles telles que la modélisation de gisements, le séquençage de l’ADN, l’analyse des protéines, les simulations de Monte-Carlo, etc. À l’instar de la série NC, la série NCv2 offre une configuration avec une interface réseau à haut débit et à faible latence optimisée pour les charges de travail d’informatique parallèle étroitement couplées.

> [!IMPORTANT]
> Pour cette famille de tailles, le quota de processeurs virtuels (cœurs) dans votre abonnement est défini au départ sur 0 dans chaque région. [Demandez une augmentation du quota de processeurs virtuels](../articles/azure-supportability/resource-manager-core-quotas-request.md) pour cette famille dans une [région disponible](https://azure.microsoft.com/regions/services/).
>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Disques de données max. |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6_v2 |6. |112 | 336 | 1 | 12 |
| Standard_NC12_v2 |12 |224 | 672 | 2 | 24 |
| Standard_NC24_v2 |24 |448 | 1344 | 4 | 32 |
| Standard_NC24r_v2* |24 |1448 | 1344 | 4 | 32 |

1 GPU = une carte P100.

*Prenant en charge RDMA

## <a name="nd-instances"></a>Instances ND

Les machines virtuelles de la série ND sont une nouveauté de la famille de GPU. Elles sont spécialement conçues pour les charges de travail d’intelligence artificielle et de Deep Learning. Elles offrent d’excellentes performances pour l’apprentissage et l’inférence. Les instances ND sont optimisées par des GPU [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf). Ces instances offrent d’excellentes performances pour les opérations à virgule flottante simple précision, et pour les charges de travail d’intelligence artificielle utilisant Microsoft Cognitive Toolkit, TensorFlow, Caffe et d’autres infrastructures. La série ND offre également une taille de mémoire GPU beaucoup plus importante (24 Go), ce qui permet d’adapter des modèles de réseaux neuronaux beaucoup plus volumineux. À l’instar de la série NC, la série ND offre une configuration avec un réseau à faible latence secondaire et à haut débit grâce à l’accès direct à la mémoire à distance (RDMA), ainsi que la connectivité InfiniBand, de sorte que vous pouvez exécuter des travaux de formation à grande échelle s’étendant sur de nombreux GPU.

> [!IMPORTANT]
> Pour cette famille de tailles, le quota de processeurs virtuels (cœurs) par région dans votre abonnement est défini au départ sur 0. [Demandez une augmentation du quota de processeurs virtuels](../articles/azure-supportability/resource-manager-core-quotas-request.md) pour cette famille dans une [région disponible](https://azure.microsoft.com/regions/services/).
>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Disques de données max. |
| --- | --- | --- | --- | --- | --- |
| Standard_ND6 |6. |112 | 336 | 1 | 12 |
| Standard_ND12 |12 |224 | 672 | 2 | 24 |
| Standard_ND24 |24 |448 | 1344 | 4 | 32 |
| Standard_ND24r* |24 |1448 | 1344 | 4 | 32 |

1 GPU = une carte P40.

*Prenant en charge RDMA

## <a name="nv-instances"></a>Instances NV



Les instances NV sont optimisées par des GPU [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) et la technologie NVIDIA GRID pour les applications de bureautique accélérées et les bureaux virtuels où les clients peuvent visualiser leurs données ou simulations. Les utilisateurs peuvent visualiser leurs flux de travail nécessitant beaucoup de graphismes sur les instances NV afin d’obtenir des fonctionnalités graphiques de qualité supérieure et exécuter par ailleurs des charges de travail simple précision comme le codage et le rendu. 

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Disques de données max. |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6. |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1 440 | 4 | 64 |

1 GPU = une moitié de carte M60.


