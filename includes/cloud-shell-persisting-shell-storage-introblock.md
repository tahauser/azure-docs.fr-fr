# <a name="persist-files-in-azure-cloud-shell"></a>Conserver des fichiers dans Azure Cloud Shell
Cloud Shell utilise le stockage de fichiers Azure pour conserver les fichiers entre les sessions.

## <a name="set-up-a-clouddrive-file-share"></a>Configurer un partage de fichiers clouddrive
Lors du premier démarrage, Cloud Shell vous invite à associer un partage de fichiers nouveau ou existant afin de conserver les fichiers entre les sessions.

> [!NOTE]
> Bash et PowerShell utilisent le même partage de fichiers. Un seul partage de fichiers peut être associé à un montage automatique dans Cloud Shell.

### <a name="create-new-storage"></a>Créer un stockage

Lorsque vous utilisez des paramètres de base et sélectionnez uniquement un abonnement, Cloud Shell crée trois ressources pour vous dans la région prise en charge la plus proche de vous :
* Groupe de ressources : `cloud-shell-storage-<region>`
* Compte de stockage : `cs<uniqueGuid>`
* Partage de fichiers : `cs-<user>-<domain>-com-<uniqueGuid>`

![Paramètre d’abonnement](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

Le partage de fichiers est monté comme un `clouddrive` dans votre répertoire `$Home`. Cette opération n’a lieu qu’une seule fois, car le partage de fichiers est automatiquement monté dans les sessions suivantes. 

Dans Bash, le partage de fichiers contient également une image de 5 Go. Automatiquement créée pour vous, celle-ci conserve les données dans votre répertoire `$Home`. 

### <a name="use-existing-resources"></a>Utiliser les ressources existantes

L’option Avancé vous permet d’associer des ressources existantes. Lorsque l’invite de configuration du stockage s’affiche, sélectionnez **Afficher les paramètres avancés** pour visualiser des options supplémentaires. Les listes déroulantes sont filtrées sur la région Cloud Shell qui vous a été attribuée, ainsi que sur les comptes de stockage localement redondant et géoredondant.

Dans Bash, les partages de fichiers existants reçoivent une image de 5 Go pour conserver votre répertoire `$Home`.

![Paramètre Groupe de ressources](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restreindre la création de ressources avec une stratégie de ressource Azure
Les comptes de stockage que vous créez dans Cloud Shell sont identifiés à l’aide de la balise `ms-resource-usage:azure-cloud-shell`. Si vous souhaitez interdire aux utilisateurs de créer des comptes de stockage par le biais de Cloud Shell, créez une [stratégie de ressource Azure pour les balises](../articles/azure-policy/json-samples.md) déclenchée par cette balise spécifique.

## <a name="supported-storage-regions"></a>Régions de stockage prises en charge
Les comptes de stockage Azure associées doivent résider dans la même région que la machine Cloud Shell sur laquelle le montage est effectué.

Pour rechercher la région qui vous est affectée, vous pouvez :
* Afficher la remarque de la boîte de dialogue des paramètres de stockage avancés
* Faire référence au nom du compte de stockage créé pour vous (ex. : `cloud-shell-storage-westus`)
* Exécuter `env` et rechercher la variable `ACC_LOCATION`

La machine Cloud Shell existe dans les régions suivantes :
|Domaine|Région|
|---|---|
|Amérique|Est des États-Unis, Sud du centre des États-Unis, Ouest des États-Unis|
|Europe|Europe du Nord, Europe de l’Ouest|
|Asie-Pacifique|Inde-Centre, Sud-Est asiatique|

