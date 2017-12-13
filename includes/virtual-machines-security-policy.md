Il est important de sécuriser votre machine virtuelle (VM) pour les applications que vous exécutez. La sécurisation des machines virtuelles peut comprendre plusieurs services et fonctionnalités Azure qui garantissent un accès sécurisé à vos machines virtuelles et le stockage sécurisé des données. Cet article donne des informations vous permettant de sécuriser votre machine virtuelle et vos applications.

## <a name="antimalware"></a>Logiciel anti-programme malveillant

Les menaces vis-à-vis des environnements cloud sont dynamiques et il faut plus que jamais maintenir une protection efficace dans le but de répondre aux exigences de conformité et de sécurité. [Microsoft Antimalware pour Azure](../articles/security/azure-security-antimalware.md) offre une fonctionnalité de protection en temps réel qui permet d’identifier et de supprimer les virus, logiciels espions et autres logiciels malveillants. Les alertes peuvent être configurées pour vous avertir lorsqu’un logiciel malveillant ou indésirable connu tente de s’installer ou de s’exécuter sur votre machine virtuelle Azure.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md) vous aide à vous empêcher, détecter et répondre aux menaces pesant sur vos machines virtuelles. Security Center fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## <a name="encryption"></a>Chiffrement

Pour renforcer la sécurité et la conformité de la machine virtuelle [Windows](../articles/virtual-machines/windows/encrypt-disks.md) et [Linux](../articles/virtual-machines/linux/encrypt-disks.md), les disques virtuels dans Azure peuvent être chiffrés. Les disques virtuels sur des machines virtuelles Windows sont chiffrés au repos à l’aide de Bitlocker. Les disques virtuels sur des machines virtuelles Linux sont chiffrés au repos à l’aide de la commande dm-crypt. 

Le chiffrement de disques virtuels dans Azure n’entraîne aucun frais. Les clés de chiffrement sont stockées dans le coffre de clés Azure à l’aide d’une protection logicielle, mais vous pouvez importer ou générer vos clés dans des modules de sécurité matériels (HSM) certifiés conformes aux normes FIPS 140-2 de niveau 2. Ces clés de chiffrement servent à chiffrer et à déchiffrer les disques virtuels connectés à votre machine virtuelle. Vous gardez le contrôle de ces clés de chiffrement et pouvez effectuer un audit de leur utilisation. Un principal de service Azure Active Directory fournit un mécanisme sécurisé pour l’émission de ces clés de chiffrement lors de la mise sous tension et hors tension des machines virtuelles.

## <a name="key-vault-and-ssh-keys"></a>Key Vault et clés SSH

Les secrets et certificats peuvent être modélisés en tant que ressources et fournies par [Key Vault](../articles/key-vault/key-vault-whatis.md). Vous pouvez utiliser Azure PowerShell pour créer des coffres de clé pour les[machines virtuelles Windows](../articles/virtual-machines/windows/key-vault-setup.md) et l’interface de ligne de commande Azure pour les [machines virtuelles Linux](../articles/virtual-machines/linux/key-vault-setup.md). Vous pouvez également créer des clés de chiffrement.

Les stratégies d’accès à un coffre de clés accordent des autorisations s’appliquant soit aux clés, soit aux secrets, soit aux certificats. Par exemple, vous pouvez donner accès aux clés à un utilisateur, mais aucune autorisation pour les secrets. Toutefois, les autorisations d’accès aux clés, aux secrets ou aux certificats concernent le niveau du coffre. En d’autres termes, la [stratégie d’accès à un coffre de clés](../articles/key-vault/key-vault-secure-your-key-vault.md) ne prend pas en charge les autorisations de niveau objet.

Lorsque vous vous connectez à des machines virtuelles, vous devez utiliser un chiffrement à clé publique pour garantir une connexion plus sûre à ces dernières. Ce processus implique un échange de clés publiques et privées à l’aide de la commande SSH (secure shell) pour vous authentifier vous-même plutôt qu’un nom d’utilisateur et un mot de passe. Les mots de passe sont vulnérables aux attaques en force brute, en particulier sur les machines virtuelles connectées à Internet comme les serveurs web. Avec une paire de clés SSH (secure shell), vous pouvez créer une [machine virtuelle Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) qui utilise des clés SSH pour l’authentification, éliminant ainsi la nécessité de recourir aux mots de passe pour la connexion. Vous pouvez également utiliser des clés SSH pour vous connecter d’une [machine virtuelle Windows](../articles/virtual-machines/linux/ssh-from-windows.md) à une machine virtuelle Linux.

## <a name="policies"></a>Stratégies

Vous pouvez utiliser des [stratégies Azure](../articles/azure-policy/azure-policy-introduction.md) pour définir le comportement souhaité des machines virtuelles [Windows](../articles/virtual-machines/windows/policy.md) et [Linux](../articles/virtual-machines/linux/policy.md) de votre organisation. Avec les stratégies, une organisation peut appliquer différentes conventions et règles à travers l'entreprise. L’application du comportement souhaité peut vous aider à atténuer les risques tout en contribuant à la réussite de l'organisation.

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Avec le [contrôle d’accès en fonction du rôle](../articles/active-directory/role-based-access-control-what-is.md), vous pouvez séparer les tâches au sein de votre équipe et accorder aux utilisateurs uniquement les accès nécessaires sur votre machine virtuelle pour accomplir leur travail. Plutôt que de donner à tous des autorisations illimitées sur la machine virtuelle, vous pouvez autoriser uniquement certaines actions. Vous pouvez configurer le contrôle d’accès pour la machine virtuelle dans le [portail Azure](../articles/active-directory/role-based-access-control-configure.md), à l’aide d’[Azure CLI](https://docs.microsoft.com/cli/azure/role), ou d’[Azure PowerShell](../articles/active-directory/role-based-access-control-manage-access-powershell.md).


## <a name="next-steps"></a>Étapes suivantes
- Suivez les étapes permettant de surveiller la sécurité de la machine virtuelle à l’aide d’Azure Security Center pour [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) ou [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).