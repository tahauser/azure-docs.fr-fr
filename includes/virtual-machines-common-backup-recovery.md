
## <a name="azure-backup"></a>Sauvegarde Azure

Pour sauvegarder des machines virtuelles Azure exécutant des charges de travail de production, utilisez la sauvegarde Azure. La sauvegarde Azure prend en charge des sauvegardes cohérentes avec les applications pour les machines virtuelles Linux et Windows. Azure Backup crée des points de récupération stockés dans des coffres de récupération géoredondants. Lorsque vous effectuez une restauration à partir d’un point de récupération, vous pouvez restaurer la machine virtuelle entière ou seulement des fichiers spécifiques. 

Pour une introduction simple et pratique à la sauvegarde Azure pour les machines virtuelles Azure, consultez le « didacticiel sur la sauvegarde des machines virtuelles Azure » pour [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) ou [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Pour plus d’informations sur le fonctionnement de la sauvegarde Azure, consultez [Planification de votre infrastructure de sauvegarde de machines virtuelles dans Azure](../articles/backup/backup-azure-vms-introduction.md).


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery protège vos machines virtuelles dans un scénario de récupération d’urgence importante, dans lequel une région entière connaît une panne en raison d’une catastrophe naturelle majeure ou d’une interruption de service généralisée. Vous pouvez configurer Azure Site Recovery pour vos machines virtuelles afin de pouvoir récupérer votre application en un seul clic en quelques minutes. Vous pouvez répliquer vers une région Azure de votre choix sans être limité à des régions appairées. 

Vous pouvez effectuer des exercices de récupération d’urgence avec des tests de basculement à la demande, sans affecter vos charges de travail de production ni la réplication continue. Créez des plans de récupération pour orchestrer le basculement et la restauration automatique de l’intégralité de l’application en cours d’exécution sur plusieurs machines virtuelles. Le plan de récupération est intégré à des runbooks Azure Automation.

Vous pouvez commencer par [répliquer vos machines virtuelles](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Captures instantanées gérées 

Dans les environnements de développement et de test, les captures instantanées constituent une option rapide et simple pour la sauvegarde de machines virtuelles qui utilisent Managed Disks. Une capture instantanée gérée est une copie intégrale en lecture seule d’un disque géré. Les captures instantanées existent indépendamment du disque source et peuvent être utilisées pour créer des disques gérés afin de régénérer une machine virtuelle. Elles sont facturées en fonction de la portion utilisée sur le disque. Par exemple, si vous créez une capture instantanée d’un disque géré avec une capacité approvisionnée de 64 Go et une taille des données utilisées réelle de 10 Go, la capture instantanée sera facturée uniquement pour la taille des données utilisées de 10 Go.  

Pour plus d’informations sur la création de captures instantanées, consultez :

* [Créer une copie d’un disque dur virtuel stocké en tant que disque géré à l’aide de la fonction Instantanés dans Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Créer une copie d’un disque dur virtuel stocké en tant que disque managé à l’aide de la fonction Instantanés dans Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Étapes suivantes
Vous pouvez essayer la sauvegarde Azure en suivant le « didacticiel sur la sauvegarde des machines virtuelles Windows » pour [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) ou [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
