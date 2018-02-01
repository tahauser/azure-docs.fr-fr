Le tableau suivant répertorie les limites associées aux différents niveaux de service (S1, S2, S3 et F1). Pour plus d'informations sur le coût de chaque *unité* dans chaque couche, consultez [la tarification du IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Ressource | S1 Standard | S2 Standard | S3 Standard | F1 Gratuit |
| --- | --- | --- | --- | --- |
| Messages par jour |400 000 |6 000 000 |300 000 000 |8 000 |
| Nombre maximum d'unités |200 |200 |10 |1 |

> [!NOTE]
> Si vous prévoyez d’utiliser plus de 200 unités avec un hub de niveau S1 ou S2, ou 10 unités avec un hub de niveau S3, veuillez contacter le support Microsoft.
> 
> 

Le tableau suivant répertorie les limites qui s'appliquent aux ressources IoT Hub :

| Ressource | Limite |
| --- | --- |
| Nombre maximal d’IoT Hubs payants par abonnement Azure |10 |
| Nombre maximal d’IoT Hubs gratuits par abonnement Azure |1 |
| Nombre maximal d’identités de périphériques<br/> renvoyées dans un seul appel |1 000 |
| Conservation maximale des messages IoT Hub pour les messages de l’appareil vers le cloud |7 jours |
| Taille maximale du message appareil-à-cloud |256 KB |
| Taille maximale du lot appareil-à-cloud |256 KB |
| Nombre maximal de messages dans le lot appareil-à-cloud |500 |
| Taille maximale du message cloud-à-appareil |64 Ko |
| Durée de vie maximale des messages cloud-à-appareil |2 jours |
| Nombre de remises maximal pour les messages  <br/> Cloud vers appareil |100 |
| Nombre de remises maximal pour les messages de commentaire  <br/> en réponse à un message Cloud vers appareil |100 |
| Durée de vie maximale des messages de commentaire  <br/> en réponse à un message Cloud vers appareil |2 jours |
| Taille maximale du jumeau d’appareil <br/> (balises, propriétés signalées et propriétés souhaitées) | 8 Ko |
| Taille maximale de la valeur de chaîne du jumeau d’appareil | 4 Ko |
| Profondeur maximale de l’objet dans le jumeau d’appareil | 5. |
| Taille maximale de la charge utile de la méthode directe | 128 Ko |
| Conservation maximale de l’historique des travaux | 30 jours |
| Nombre maximal de travaux simultanés | 10 (pour S3), 5 pour (S2), 1 (pour S1) |
| Nombre maximal de points de terminaison supplémentaires | 10 (pour S1, S2, S3) |
| Nombre maximal de règles de routage de messages | 100 (pour S1, S2, S3) |


> [!NOTE]
> Si vous avez besoin de plus de 10 IoT Hubs payants dans un abonnement Azure, contactez le support technique Microsoft.


> [!NOTE]
> Actuellement, le nombre maximal d’appareils que vous pouvez connecter à un IoT Hub unique est 500 000. Si vous souhaitez augmenter cette limite, contactez le [support Microsoft](https://azure.microsoft.com/en-us/support/options/).

Le service IoT Hub limite les demandes cas de dépassement des quotas suivants :

| Limitation | Valeur par hub |
| --- | --- |
| Opérations du registre d’identité <br/> (créer, récupérer, répertorier, mettre à jour, supprimer), <br/> importation / exportation en bloc ou individuelle |83,33/s/unité (5 000/min/unité) (pour S3) <br/> 1,67/s/unité (100/min/unité) (pour S1 et S2). |
| Connexions d’appareils |6 000/s/unité (pour S3), 120/s/unité (pour S2), 12/s/unité (pour S1). <br/>Minimum de 100/s. |
| Envois appareil-à-cloud |6 000/s/unité (pour S3), 120/s/unité (pour S2), 12/s/unité (pour S1). <br/>Minimum de 100/s. |
| Envois cloud-à-appareil | 83,33/s/unité (5000/min/unité) (pour S3), 1,67/s/unité (100/min/unité) (pour S1 et S2). |
| Réceptions cloud-à-appareil |833,33/s/unité (50 000/min/unité) (pour S3), 16,67/s/unité (1 000/min/unité) (pour S1 et S2). |
| Opérations de téléchargement de fichier |83,33 notifications de chargement de fichier/s/unité (5 000/min/unité) (pour S3), 1,67 notifications de chargement de fichier/s/unité (100/min/unité) (pour S1 et S2). <br/> 10000 URI de signature d’accès partagé peuvent être générés à la fois pour un compte Azure Storage.<br/> 10 URI de signature d’accès partagé/appareil peuvent être générés à la fois. |
| Méthodes directes | 24 Mo/s/unité (pour S3), 480 Ko/s/unité (pour S2), 160 Ko/s/unité (pour S1)<br/> Basée sur une limitation de taille du compteur de 8 Ko. |
| Lectures de jumeaux d’appareil | 50/s/unité (pour S3), 10/s ou 1/s/unité maximum (pour S2), 10/s (pour S1) |
| Mises à jour de jumeaux d’appareils | 50/s/unité (pour S3), 10/s ou 1/s/unité maximum (pour S2), 10/s (pour S1) |
| Opérations de travaux <br/> (créer, mettre à jour, répertorier, supprimer) | 83,33/s/unité (5 000/min/unité) (pour S3), 1,67/s/unité (100/min/unité) (pour S2), 1,67/s/unité (100/min/unité) (pour S1) |
| Débit d’opérations de travaux par appareil | 50/s/unité (pour S3), 10/s ou 1/s/unité maximum (pour S2), 10/s (pour S1) |
