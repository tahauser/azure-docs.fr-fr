| **Matériel** | |
| --- |---|
| Nombre de cœurs de processeur| 8 |
| RAM| 12 Go|
| Nombre de disques | 3 <br><br> - Disque du système d’exploitation<br> - Disque de cache du serveur de traitement<br> - Lecteur de rétention (pour la restauration automatique)|
| Espace disque disponible (cache du serveur de traitement) | 600 Go
| Espace disque disponible (disque de rétention) | 600 Go|
| **Logiciel** | |
| Version de système d’exploitation | Windows Server 2012 R2 <br> Windows Server 2016 |
| Paramètres régionaux du système d’exploitation | Anglais (en-us)|
| Version VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Rôles Windows Server | Ne pas activer les rôles suivants : <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Stratégies de groupe| Les stratégies de groupe suivantes ne doivent pas être activées sur le serveur <br> - Empêcher l’accès à l’invite de commandes <br> - Empêcher l’accès aux outils de modification du Registre <br> - Logique de confiance pour les pièces jointes <br> - Activer l’exécution des scripts <br> **Remarque :** Vous trouverez plus d’informations sur ces stratégies de groupe [ici](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx).|
| Configuration d’Internet Information Service (IIS) | - Aucun site web par défaut pré-existant <br> - Activer l’[authentification anonyme](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> - Activer le paramètre [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx)  <br> - Aucune application/aucun site web existants ne doivent écouter le port 443<br>|
| **Réseau** | |
| Type de carte d’interface réseau | VMXNET3 (en cas de déploiement comme machine virtuelle VMware) |
| Type d’adresse IP | Statique |
| Accès à Internet | Le serveur doit être en mesure d’accéder à l’URL suivante, directement ou via un serveur proxy : <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (non requis pour les serveurs de traitement à montée en charge) <br> - time.nist.gov <br> - time.windows.com |
| Ports | 443 (Orchestration du canal de contrôle)<br>9443 (Transport de données)|
