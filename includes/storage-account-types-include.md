Il existe deux types de comptes de stockage :

### <a name="general-purpose-storage-accounts"></a>Comptes de stockage à usage général
Un compte de stockage à usage général vous donne accès aux services Azure Storage telles que les tables, files d’attente, fichiers, objets blob et disques de machine virtuelle Azure au sein d’un compte unique. Ce type de compte de stockage offre deux niveaux de performances :

* Un niveau de performances de stockage standard qui vous permet de stocker des tables, files d’attente, fichiers, objets blob et disques de machine virtuelle Azure.
* Un niveau de performances de stockage premium qui prend actuellement en charge les disques de machine virtuelle Azure uniquement. Pour une présentation détaillée de Premium Storage, consultez [Premium Storage : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/virtual-machines/windows/premium-storage.md) .

### <a name="blob-storage-accounts"></a>Comptes de stockage d’objets blob
Un compte de stockage d’objets blob est un compte de stockage spécialisé pour le stockage des données non structurées en tant qu’objets blob dans Azure Storage. Les comptes de stockage d’objets blob sont similaires à vos comptes de stockage à usage général existants et offrent les excellents niveaux de durabilité, disponibilité, évolutivité et performances dont vous bénéficiez aujourd’hui. Ils assurent notamment la cohérence d’API à 100 % pour les objets blob de blocs et d’ajout. Pour les applications qui requièrent uniquement le stockage d’objets blob de blocs ou d’objets blob d’ajout, nous recommandons d’utiliser des comptes de stockage d’objets blob.

> [!NOTE]
> Les comptes de stockage d’objets blob prennent en charge uniquement les objets blob de blocs et d’ajout, mais pas les objets blob de pages.
> 
> 

Les comptes de stockage d’objets blob exposent l’attribut **Access Tier** , qui peut être spécifié lors de la création du compte et modifié ultérieurement si nécessaire. Il existe deux types de niveaux d’accès qui peuvent être spécifiés en fonction de votre modèle d’accès aux données :

* Un niveau d’accès **chaud** qui indique que les objets du compte de stockage seront sollicités plus fréquemment. Il permet de stocker des données moyennant un coût d’accès inférieur.
* Un niveau d’accès **froid** qui indique que les objets du compte de stockage seront sollicités moins fréquemment. Il permet de stocker des données moyennant un coût de stockage inférieur.

Si le modèle d’utilisation de vos données est modifié, vous pouvez également basculer entre ces niveaux d’accès à tout moment. La modification du niveau d’accès peut entraîner des frais supplémentaires. Consultez [Comptes de stockage d’objets blob - Tarification et facturation](../articles/storage/common/storage-account-options.md#pricing-and-billing) pour plus de détails.

Pour plus d’informations sur les comptes de stockage d’objets blob, voir [Stockage d’objets blob Azure : niveaux froid et chaud](../articles/storage/blobs/storage-blob-storage-tiers.md)

Pour pouvoir créer un compte de stockage, vous devez posséder un abonnement Azure, c’est-à-dire un plan qui vous donne accès à divers services Azure. Pour la prise en main d’Azure, vous pouvez bénéficier d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/). Quand vous décidez d’acheter un plan d’abonnement, vous avez le choix entre plusieurs [options d’achat](https://azure.microsoft.com/pricing/purchase-options/). Si vous êtes [abonné à MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vous bénéficiez de crédits mensuels gratuits que vous pouvez utiliser avec les services Azure, y compris Azure Storage. Pour plus d’informations sur la tarification des licences en volume, consultez la page [Prix appliqués à Azure Storage ](https://azure.microsoft.com/pricing/details/storage/) .

Pour apprendre à créer un compte de stockage, consultez [Créer un compte de stockage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) . Un seul abonnement vous permet de créer jusqu’à 200 comptes de stockage uniques. Pour plus d’informations sur les limites du compte de stockage, consultez la page [Objectifs de performance et évolutivité d’Azure Storage](../articles/storage/common/storage-scalability-targets.md) .

