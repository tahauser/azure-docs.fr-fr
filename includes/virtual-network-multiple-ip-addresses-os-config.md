## <a name="os-config"></a>Ajouter des adresses IP à un système d’exploitation de machine virtuelle

Connectez-vous à une machine virtuelle que vous avez créée à l’aide de plusieurs adresses IP privées. Vous devez ajouter manuellement toutes les adresses IP privées (y compris l’adresse principale) que vous avez ajoutées à la machine virtuelle. Effectuez les étapes suivantes dans le système d’exploitation de votre machine virtuelle :

### <a name="windows"></a>Windows

1. Tapez *ipconfig /all*à l’invite de commandes.  Seule l’adresse IP privée *principale* est visible (via DHCP).
2. Saisissez *ncpa.cpl* dans l’invite de commandes pour ouvrir la fenêtre **Connexions réseau**.
3. Ouvrez les propriétés de la carte réseau appropriée : **Connexion au réseau local**.
4. Double-cliquez sur Internet Protocol version 4 (IPv4).
5. Cliquez sur **Utiliser l’adresse IP suivante** et entrez les valeurs suivantes :

    * **Adresse IP**: entrez l’adresse IP privée *principale* .
    * **Masque de sous-réseau**: définissez cette option en fonction de votre sous-réseau. Par exemple, si le sous-réseau est un sous-réseau /24, le masque de sous-réseau est 255.255.255.0.
    * **Passerelle par défaut**: première adresse IP du sous-réseau. Si votre sous-réseau est 10.0.0.0/24, l’adresse IP de la passerelle est 10.0.0.1.
    * Cliquez sur **Utiliser l’adresse de serveur DNS suivante** et saisissez les valeurs ci-dessous :
        * **Serveur DNS préféré** : saisissez 168.63.129.16 si vous n’utilisez pas votre propre serveur DNS.  Si vous utilisez votre propre serveur DNS, entrez l’adresse IP de votre serveur.
    * Cliquez sur le bouton **Avancé** et ajoutez des adresses IP supplémentaires. Ajoutez chacune des adresses IP privées secondaires de l’étape 8 à l’interface réseau avec le même sous-réseau que celui de l’adresse IP principale.
        >[!WARNING] 
        >Si vous ne suivez pas les étapes précédentes correctement, vous risquez de perdre la connectivité avec votre machine virtuelle. Assurez-vous que les informations entrées à l’étape 5 sont exactes avant de continuer.

    * Cliquez sur **OK** pour fermer les paramètres TCP/IP, puis sur **OK** à nouveau pour fermer les paramètres de la carte réseau. Votre connexion RDP est rétablie.

6. Tapez *ipconfig /all*à l’invite de commandes. Toutes les adresses IP que vous avez ajoutées sont affichées et le protocole DHCP est désactivé.
7. Configurez Windows pour utiliser l’adresse IP privée de la configuration IP principale dans Azure en tant qu’adresse IP principale pour Windows. Pour plus d’informations, consultez [Aucun accès à Internet à partir de la machine virtuelle Windows Azure qui possède plusieurs adresses IP](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse). 


### <a name="validation-windows"></a>Validation (Windows)

Pour être sûr de pouvoir vous connecter à Internet à partir de votre configuration IP secondaire via l’adresse IP associée, une fois que vous avez ajoutée celle-ci correctement à l’aide des étapes ci-dessus, utilisez la commande suivante :

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>Pour les configurations IP secondaires, vous pouvez uniquement exécuter une commande ping sur Internet si une adresse IP publique est associée à la configuration. Pour les configurations IP principales, une adresse IP publique n’est pas requise pour exécuter une commande ping sur Internet.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Ouvrez une fenêtre de terminal.
2. Assurez-vous d’être l’utilisateur root. Si ce n’est pas le cas, saisissez la commande suivante :

    ```bash
    sudo -i
    ```

3. Mettez à jour le fichier de configuration de l’interface réseau (en supposant que « eth0 » est utilisé).

    * Conservez l’élément de ligne existant pour dhcp. L’adresse IP principale reste configurée telle qu’elle était précédemment.
    * Ajoutez une configuration pour une adresse IP statique supplémentaire à l’aide des commandes suivantes :

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    Un fichier .cfg doit s’afficher.
4. Ouvrez le fichier. Les lignes suivantes doivent figurer à la fin du fichier :

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. Ajoutez les lignes suivantes après les lignes qui existent dans ce fichier :

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    netmask <your subnet mask>
    ```

6. Enregistrez le fichier à l’aide de la commande suivante :

    ```bash
    :wq
    ```

7. Réinitialisez l’interface réseau à l’aide de la commande suivante :

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > Exécutez les scripts ifup et ifdown sur la même ligne si vous utilisez une connexion à distance.
    >

8. Vérifiez que l’adresse IP est ajoutée à l’interface réseau à l’aide de la commande suivante :

    ```bash
    ip addr list eth0
    ```

    Vous devez voir l’adresse IP que vous avez ajoutée à la liste.

### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS, etc.)

1. Ouvrez une fenêtre de terminal.
2. Assurez-vous d’être l’utilisateur root. Si ce n’est pas le cas, saisissez la commande suivante :

    ```bash
    sudo -i
    ```

3. Entrez votre mot de passe et suivez les instructions qui s’affichent. Une fois que vous êtes l’utilisateur root, accédez au dossier Scripts réseau avec la commande suivante :

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Répertoriez les fichiers ifcfg connexes à l’aide de la commande suivante :

    ```bash
    ls ifcfg-*
    ```

    Vous devez voir *ifcfg-eth0* dans la liste de fichiers.

5. Pour ajouter une adresse IP, créez un fichier de configuration pour celle-ci comme indiqué ci-dessous. Notez qu’un fichier doit être créé pour chaque configuration IP.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Ouvrez le fichier *ifcfg-eth0:0* à l’aide de la commande suivante :

    ```bash
    vi ifcfg-eth0:0
    ```

7. Ajoutez du contenu dans le fichier, *eth0:0* dans cet exemple, à l’aide de la commande suivante. Veillez à mettre à jour les informations en fonction de votre adresse IP.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. Enregistrez le fichier à l’aide de la commande suivante :

    ```bash
    :wq
    ```

9. Redémarrez les services réseau et vérifiez que les modifications ont été appliquées en exécutant les commandes suivantes :

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    Vous devez voir l’adresse IP que vous avez ajoutée à la liste, dans le cas présent *eth0:0*.

### <a name="validation-linux"></a>Validation (Linux)

Pour être sûr de pouvoir vous connecter à Internet à partir de votre configuration IP secondaire via l’adresse IP associée, utilisez la commande suivante :

```bash
ping -I 10.0.0.5 hotmail.com
```
>[!NOTE]
>Pour les configurations IP secondaires, vous pouvez uniquement exécuter une commande ping sur Internet si une adresse IP publique est associée à la configuration. Pour les configurations IP principales, une adresse IP publique n’est pas requise pour exécuter une commande ping sur Internet.

Pour les machines virtuelles Linux, lorsque vous tentez de valider la connectivité sortante à partir d’une carte réseau secondaire, il se peut que vous deviez ajouter des itinéraires appropriés. Pour ce faire, de nombreuses options s’offrent à vous. Reportez-vous à la documentation appropriée pour votre distribution Linux. Voici une méthode pour effectuer cette opération :

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Veillez à remplacer :
    - **10.0.0.5** par l’adresse IP privée à laquelle une adresse IP publique est associée
    - **10.0.0.1** par votre passerelle par défaut
    - **eth2** le nom de votre carte réseau secondaire
