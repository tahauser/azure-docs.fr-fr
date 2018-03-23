>[!NOTE]
>Pour les ressources qui ne sont pas fixes, vous pouvez demander d’augmenter les quotas en ouvrant un ticket de support. Ne créez **pas** d’autres comptes Azure Media Services pour obtenir des limites supérieures.

| Ressource | Limite par défaut | 
| --- | --- | 
| Comptes AMS (Azure Media Services) dans un seul abonnement | 25 (fixe) |
| Unités réservées de média (RU) par compte AMS |25 (S1)<br/>10 (S2, S3) <sup>(1)</sup> | 
| Travaux par compte AMS | 50,000<sup>(2)</sup> |
| Tâches chaînées par travail | 30 (fixe) |
| Actifs par compte AMS | 1 000 000|
| Actifs par tâche | 50 |
| Actifs par travail | 100 |
| Localisateurs uniques associés à un actif à un moment donné | 5<sup>(4)</sup> |
| Canaux en direct par compte AMS  |5.|
| Programmes dans un état Arrêté par canal  |50|
| Programmes en cours d’exécution par canal  |3|
| Points de terminaison de diffusion en continu en cours d’exécution par compte AMS|2|
| Unités de diffusion en continu par point de terminaison de diffusion en continu  |10 |
| Comptes de stockage | 1 000<sup>(5)</sup> (fixe) |
| Stratégies | 1,000,000<sup>(6)</sup> |
| Taille du fichier| Dans certains scénarios, la taille maximale des fichiers pris en charge pour le traitement dans Media Services est soumise à une limite. <sup>7</sup> |
  
<sup>1</sup> Si vous changez le type (par exemple, de S2 à S1), les limites d’unités réservées maximales sont réinitialisées.

<sup>2</sup> Ce nombre comprend les travaux en file d’attente, terminés, actifs et annulés. Il n’inclut pas les travaux supprimés. Vous pouvez supprimer les anciens travaux à l’aide de **IJob.Delete** ou de la requête HTTP **DELETE**.

Depuis le 1er avril 2017, les enregistrements de travaux de votre compte qui ont plus de 90 jours sont automatiquement supprimés, ainsi que leurs enregistrements de tâches associés, même si le nombre total d’enregistrements est inférieur au quota maximal. Si vous devez archiver les informations sur le travail/la tâche, vous pouvez utiliser le code décrit [ici](../articles/media-services/media-services-dotnet-manage-entities.md).

<sup>3</sup> Lors d’une requête visant à lister les entités de travail, un maximum de 1 000 travaux sont retournés par requête. Si vous souhaitez effectuer le suivi de l’ensemble des travaux soumis, vous pouvez utiliser top/skip comme décrit dans [Options de requête du système OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Les localisateurs ne sont pas conçus pour gérer le contrôle d’accès par utilisateur. Pour accorder différents droits d’accès aux utilisateurs, utilisez les solutions de gestion des droits numériques (DRM). Pour plus d’informations, consultez [cette](../articles/media-services/media-services-content-protection-overview.md) section.

<sup>5</sup> Les comptes de stockage doivent provenir du même abonnement Azure.

<sup>6</sup> Un nombre limite de 1 000 000 a été défini pour les différentes stratégies AMS (par exemple, pour la stratégie de localisateur ou pour ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Vous devez appliquer le même ID de stratégie si vous utilisez toujours le même nombre de jours, les mêmes autorisations d’accès, etc. Pour plus d’informations et un exemple, consultez [cette](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) section.

<sup>7</sup>Si vous chargez du contenu dans un actif dans Azure Media Services afin de le traiter avec l’un des processeurs multimédias du service (c’est-à-dire des encodeurs comme Media Encoder Standard et Media Encoder Premium Workflow, ou des moteurs d’analyse comme Face Detector), vous devez connaître les tailles de fichiers maximales prises en charge. 

La taille maximale prise en charge pour un objet blob est actuellement de 5 To dans Stockage Blob Azure. Toutefois, des limites supplémentaires sont applicables dans Azure Media Services en fonction des tailles de machine virtuelle utilisées par le service. Le tableau suivant indique les limites pour chacune des unités réservées Multimédia (S1, S2, S3). Si votre fichier source dépasse la limite définie dans le tableau, votre travail d’encodage échoue. Si vous encodez des sources de résolution 4K de longue durée, vous devez obligatoirement utiliser des unités réservées Multimédia S3 afin d’obtenir les performances nécessaires. Si vous avez du contenu 4K qui dépasse la limite de 260 Go sur les unités réservées Multimédia S3, contactez-nous à l’adresse amshelp@microsoft.com afin d’identifier des solutions d’atténuation potentielles permettant de prendre en charge votre scénario.

| Types d’unités réservées de média | Taille maximale en entrée (Go)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
