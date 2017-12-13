1. Copiez le programme d’installation dans un dossier local (par exemple C:\Temp) sur le serveur que vous souhaitez protéger. Exécutez les commandes suivantes en tant qu’administrateur à l’invite de commandes :

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Pour installer le service Mobilité, exécutez la commande suivante :

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```
3. Désormais, l’agent doit être inscrit auprès du serveur de configuration.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe”  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Arguments de ligne de commande du programme d’installation du service Mobilité

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Paramètre|Type|Description|Valeurs possibles|
|-|-|-|-|
|/Role|Obligatoire|Spécifie si le service Mobilité (MS) doit être installé ou si MasterTarget(MT) doit être installé|MS </br> MT|
|/InstallLocation|Facultatif|Emplacement où est installé le service Mobilité|N’importe quel dossier sur l’ordinateur|
|/Platform|Obligatoire|Spécifie la plateforme sur laquelle le service Mobilité est installé </br> </br>- **VMware** : utilisez cette valeur si vous installez le service Mobilité sur une machine virtuelle exécutée sur des *hôtes VMware vSphere ESXi*, des *hôtes Hyper-V* et des *serveurs physiques* </br> - **Azure** : utilisez cette valeur si vous installez l’agent sur une machine virtuelle Azure IaaS| VMware </br> Les tables Azure|
|/Silent|Facultatif|Précise d’exécuter le programme d’installation en mode silencieux| N/D|

>[!TIP]
> Les journaux d’installation se trouvent sous %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log

#### <a name="mobility-service-registration-command-line-arguments"></a>Arguments de ligne de commande de l’inscription du service Mobilité

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Paramètre|Type|Description|Valeurs possibles|
  |-|-|-|-|
  |/CSEndPoint |Obligatoire|Adresse IP du serveur de configuration| Une adresse IP valide|
  |/PassphraseFilePath|Obligatoire|Emplacement de la phrase secrète |N’importe quel chemin d’accès UNC ou local valide|


>[!TIP]
> Les journaux AgentConfiguration se trouvent sous %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log
