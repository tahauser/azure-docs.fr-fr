Le stockage géoredondant (GRS) est conçu pour fournir au moins 99,99999999999999 % de durabilité des objets sur une année données en répliquant vos données vers une région secondaire se situant à des centaines de kilomètres de la région primaire. Si le GRS est activé pour votre compte de stockage, vos données restent durables, même en cas de panne régionale totale ou d’incident empêchant la récupération depuis la région primaire.

Pour un compte de stockage avec GRS activé, une mise à jour est d’abord appliquée dans la région primaire. La mise à jour est ensuite répliquée également de manière asynchrone dans la région secondaire.

Avec le stockage GRS, les régions primaire et secondaire gèrent les réplicas sur plusieurs domaines d’erreur et domaines de mise à niveau distincts, au sein d’une unité d’échelle de stockage comme décrit pour le stockage LRS.

Considérations :

* Étant donné que la réplication asynchrone implique un délai, il est possible que, en cas de sinistre régional, les modifications n’ayant pas encore été répliquées dans la région secondaire soient perdues, si les données ne peuvent pas être récupérées à partir de la région primaire.
* Le réplica n’est disponible que si Microsoft lance le basculement vers la région secondaire. Si Microsoft initie un basculement vers la région secondaire, vous obtiendrez un accès en lecture et écriture à ces données une fois le basculement effectué. Pour plus d’informations, consultez [Conseils sur la récupération d’urgence](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Si une application souhaite lire du contenu à partir de la région secondaire, l’utilisateur doit activer le stockage RA-GRS.

Lorsque vous créez un compte de stockage, vous sélectionnez la région primaire pour le compte. La région secondaire est déterminée en fonction de la région primaire et ne peut pas être modifiée. Le tableau suivant montre les paires de régions primaires et secondaires.

| Primaire | Secondaire |
| --- | --- |
| États-Unis - partie centrale septentrionale | Centre-Sud des États-Unis |
| Centre-Sud des États-Unis | États-Unis - partie centrale septentrionale |
| Est des États-Unis | Ouest des États-Unis |
| Ouest des États-Unis | Est des États-Unis |
| Est des États-Unis 2 | Centre des États-Unis |
| Centre des États-Unis | Est des États-Unis 2 |
| Europe du Nord | Europe de l'Ouest |
| Europe de l'Ouest | Europe du Nord |
| Asie du Sud-Est | Est de l'Asie |
| Est de l'Asie | Asie du Sud-Est |
| Chine orientale | Chine du Nord |
| Chine du Nord | Chine orientale |
| Est du Japon | Ouest du Japon |
| Ouest du Japon | Est du Japon |
| Sud du Brésil | Centre-Sud des États-Unis |
| Est de l’Australie | Sud-est de l’Australie |
| Sud-est de l’Australie | Est de l’Australie |
| Sud de l'Inde | Inde-Centre |
| Inde-Centre | Sud de l'Inde |
| Inde-Ouest | Sud de l'Inde |
| Gouvernement américain - Iowa | Gouvernement américain - Virginie |
| Gouvernement américain - Virginie | Gouvernement des États-Unis – Texas |
| Gouvernement des États-Unis – Texas | Gouvernement des États-Unis – Arizona |
| Gouvernement des États-Unis – Arizona | Gouvernement des États-Unis – Texas |
| Centre du Canada | Est du Canada |
| Est du Canada | Centre du Canada |
| Ouest du Royaume-Uni | Sud du Royaume-Uni |
| Sud du Royaume-Uni | Ouest du Royaume-Uni |
| Centre de l’Allemagne | Nord-Est de l’Allemagne |
| Nord-Est de l’Allemagne | Centre de l’Allemagne |
| Ouest des États-Unis 2 | Centre-Ouest des États-Unis |
| Centre-Ouest des États-Unis | Ouest des États-Unis 2 |

Pour obtenir des informations à jour sur les régions prises en charge par Azure, voir [Régions Azure](https://azure.microsoft.com/regions/).

>[!NOTE]  
> La région secondaire du Gouvernement des États-Unis - Virginie est le Gouvernement des États-Unis - Texas. Auparavant, il s’agissait du Gouvernement des États-Unis - Iowa. Les comptes de stockage utilisant toujours la région secondaire Gouvernement des États-Unis - Iowa sont en cours de migration vers la région secondaire Gouvernement des États-Unis - Texas.
>
>
