## <a name="supported-distributions-and-drivers"></a>Distributions et pilotes pris en charge


### <a name="nc-ncv2-and-nd-instances---nvidia-cuda-drivers"></a>Instances NC, NCv2 et ND : pilotes NVIDIA CUDA
| Distribution | Pilote |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 ou 7.4<br/><br/> CentOS 7.3 ou 7.4 | NVIDIA CUDA 9.1, branche pilote R390 |

> [!IMPORTANT]
> Assurez-vous que vous installez ou mettez à niveau en utilisant les derniers pilotes CUDA pour votre distribution. Les pilotes antérieurs à la version R390 peuvent présenter des problèmes avec les noyaux Linux mis à jour.
>

### <a name="nv-instances---nvidia-grid-drivers"></a>Instances NV - pilotes NVIDIA GRID


| Distribution | Pilote |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>Basé sur CentOS 7.3 | NVIDIA GRID 5.2, branche pilote R384|

> [!NOTE]
> Microsoft redistribue les programmes d’installation du pilote NVIDIA GRID pour les machines virtuelles NV. Installez uniquement ces pilotes GRID sur des machines virtuelles Azure NV. Ces pilotes incluent les licences des logiciels GRID Virtual GPU dans Azure.
>

> [!WARNING] 
> L’installation de logiciels tiers sur des produits Red Hat peut affecter les conditions de prise en charge de Red Hat. Consultez l’[article de la Base de connaissances Red Hat](https://access.redhat.com/articles/1067).
>
