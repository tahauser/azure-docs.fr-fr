| **Composant** | **Prérequis** |
| --- |---|
| Cœurs d’unité centrale| 8 |
| RAM | 12 Go|
| Nombre de disques | 3, y compris le disque du système d’exploitation, le disque de cache du serveur de processus et le lecteur de conservation pour la restauration automatique |
| Espace disque disponible (cache du serveur de traitement) | 600 Go
| Espace disque disponible (disque de rétention) | 600 Go|
| Système d’exploitation  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Paramètres régionaux du système d’exploitation | Anglais (en-us)|
| Version VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Rôles Windows Server | N’activez pas ces rôles : <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Stratégies de groupe| N’activez pas ces stratégies de groupe : <br> - Empêcher l’accès à l’invite de commandes <br> - Empêcher l’accès aux outils de modification du Registre <br> - Logique de confiance pour les pièces jointes <br> - Activer l’exécution des scripts <br> [En savoir plus](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| IIS | - Aucun site web par défaut préexistant <br> - Activer l’[authentification anonyme](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> - Activer le paramètre [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx)  <br> - Aucune application/aucun site web préexistants ne doivent écouter le port 443<br>|
| Type de carte réseau | VMXNET3 (en cas de déploiement comme machine virtuelle VMware) |
| Type d’adresse IP | statique |
| Accès à Internet | Le serveur doit également accéder à ces URL : <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (non nécessaire pour les serveurs de processus de scale-out) <br> - time.nist.gov <br> - time.windows.com |
| Ports | 443 (Orchestration du canal de contrôle)<br>9443 (Transport de données)|
