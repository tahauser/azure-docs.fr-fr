Une machine virtuelle Azure prend en charge l'attachement d'un certain nombre de disques de données. Cet article décrit les objectifs de scalabilité et de performances des disques de données d’une machine virtuelle. Utilisez ces objectifs pour déterminer le nombre et le type de disques dont vous avez besoin pour répondre à vos besoins de performances et de capacité. 

> [!IMPORTANT]
> Pour des performances optimales, limitez le nombre de disques fortement utilisés attachés à la machine virtuelle pour éviter les limitations possibles. Si tous les disques attachés ne sont pas fortement utilisés en même temps, la machine virtuelle peut prendre en charge un plus grand nombre de disques.

* **Pour Azure Managed Disks :** la limite des disques managés est exprimée par région et par type de disque. La limite maximale, ainsi que la limite par défaut, s’élèvent à 10 000 disques managés par région et par type de disque pour un abonnement. Par exemple, vous pouvez créer jusqu’à 10 000 disques managés standard et jusqu’à 10 000 disques managés Premium au sein d’un abonnement et d’une région.

    Les instantanés et les images gérés sont comptabilisés par rapport à la limite de disques managés.

* **Pour les comptes de stockage standard :** un compte de stockage standard a un taux de demandes total maximal de 20 000 opérations d'E/S par seconde. Le nombre d'opérations d'E/S par seconde sur l'ensemble de vos disques de machine virtuelle dans un compte de stockage standard ne doit pas dépasser cette limite.
  
    Vous pouvez calculer approximativement le nombre de disques fortement sollicités pris en charge par un seul compte de stockage standard en vous basant sur la limite du taux de demandes. Par exemple, pour une machine virtuelle de niveau de base, le nombre maximal de disques fortement sollicités est d’environ 66 (20 000/300 opérations d’E/S par seconde par disque) et, pour une machine virtuelle de niveau Standard, il est d’environ 40 (20 000/500 opérations d’E/S par seconde par disque). 

* **Pour les comptes de stockage premium :** un compte de stockage premium a un débit total maximum de 50 Gbits/s. Le débit total sur l'ensemble de vos disques de machine virtuelle ne doit pas dépasser cette limite.

