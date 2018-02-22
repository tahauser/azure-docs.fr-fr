1. Copiez le programme d’installation dans un dossier local (par exemple /tmp) sur le serveur que vous souhaitez protéger. Dans un terminal, exécutez les commandes suivantes :
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Pour installer le service Mobilité, exécutez la commande suivante :

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Une fois l’installation terminée, le service Mobilité doit être inscrit sur le serveur de configuration. Exécutez la commande suivante pour inscrire le service Mobilité sur le serveur de configuration.

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Ligne de commande du programme d’installation du service Mobilité

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Paramètre|type|DESCRIPTION|Valeurs possibles|
|-|-|-|-|
|-r |Obligatoire|Spécifie si le service Mobilité (MS) doit être installé ou si MasterTarget(MT) doit être installé|MS </br> MT|
|-d |Facultatif|Emplacement où sera installé le service Mobilité|/usr/local/ASR|
|-v|Obligatoire|Spécifie la plateforme sur laquelle le service Mobilité est installé </br> </br>- **VMware : utilisez cette valeur si vous installez le service Mobilité sur une machine virtuelle exécutée sur des *hôtes VMware vSphere ESXi*, des hôtes *Hyper-V et des *serveurs physiques* </br> - **Azure : utilisez cette valeur si vous installez l’agent sur une machine virtuelle Azure IaaS| VMware </br> Azure|
|-q|Facultatif|Spécifie d’exécuter le programme d’installation en mode silencieux| N/A|


#### <a name="mobility-service-configuration-command-line"></a>Ligne de commande de configuration du service Mobilité

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Paramètre|type|DESCRIPTION|Valeurs possibles|
|-|-|-|-|
|-i |Obligatoire|IP du serveur de configuration|Une adresse IP valide|
|-P |Obligatoire|Chemin d’accès complet du fichier dans lequel la phrase secrète de connexion est enregistrée|N’importe quel dossier valide|
