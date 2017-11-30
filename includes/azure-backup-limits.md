
Les limites suivantes s’appliquent à la sauvegarde Azure.

| Identificateur de la limite | Limite par défaut |
| --- | --- |
| Nombre de serveurs/machines pouvant être enregistrés pour chaque coffre |50 pour Windows Server/Client/SCDPM  <br/> 200 pour les machines virtuelles IaaS |
| Taille d’une source de données pour les données stockées dans le stockage de l’archivage Azure |54 400 Go max<sup>1</sup> |
| Nombre de coffres de sauvegarde pouvant être créés dans chaque abonnement Azure |25 (coffres de sauvegarde) <br/> 25 coffres Recovery Services par région |
| Nombre de sauvegardes pouvant être planifiées par jour |3 par jour pour Windows Server/Client  <br/> 2 par jour pour SCDPM <br/> Une fois par jour pour les machines virtuelles IaaS |
| Disques de données attachés à une machine virtuelle Azure pour la sauvegarde |16 |
| Taille du disque de données attaché à une machine virtuelle Azure pour la sauvegarde| 1023 Go <sup>2</sup>|

* <sup>1</sup>La limite de 54 400 Go ne s’applique pas à la sauvegarde de la machine virtuelle IaaS.
* <sup>2</sup> Nous disposons d’une [version préliminaire privée](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a?redir=0) qui prend en charge les disques non managés jusqu'à 4 To. 

