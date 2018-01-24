## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

* Pour connaître la disponibilité des machines virtuelles, consultez [Disponibilité des produits par région](https://azure.microsoft.com/en-us/regions/services/).

* Les machines virtuelles de série N ne peuvent être déployées que dans le modèle de déploiement Resource Manager.

* Lorsque vous créez une machine virtuelle de série N à l’aide du portail Azure, dans le panneau **Informations de base**, sélectionnez un **type de disque de machine virtuelle** **HDD**. Pour choisir une taille de série N disponible, dans le panneau **Taille**, cliquez sur **Afficher tout**.

* Les machines virtuelles NC et NV ne prennent pas en charge les disques de machines virtuelles sauvegardés par le stockage Premium Azure.

* Si vous voulez déployer plus de quelques machines virtuelles de série N, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat. Si vous utilisez un [compte gratuit Azure](https://azure.microsoft.com/free/), vous pouvez seulement utiliser un nombre limité de cœurs de calcul Azure.

* Vous devrez peut-être augmenter le quota de cœurs (par région) dans votre abonnement Azure et le quota séparé des cœurs NC, NCv2, ND ou NV. Pour demander une augmentation de quota, [ouvrez une demande de service clientèle en ligne](../articles/azure-supportability/how-to-create-azure-support-request.md) gratuitement. Les limites par défaut peuvent varier en fonction de la catégorie de votre abonnement.

* Une image de machine virtuelle que vous pouvez déployer sur les machines virtuelles de série N est la [machine virtuelle Science des données Azure](../articles/machine-learning/data-science-virtual-machine/overview.md). De nombreux outils de science des données et d’apprentissage approfondi populaires sont déjà installés et configurés sur la machine virtuelle Science des données. Des pilotes GPU NVIDIA Tesla sont également préinstallés.





