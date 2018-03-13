

## <a name="what-is-happening"></a>Ce qui se passe

Une vulnérabilité de sécurité au niveau du matériel à l’échelle du secteur tout entier a été [annoncée le 3 janvier](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html). Assurer la sécurité des clients est toujours notre priorité et nous suivons cinq étapes pour nous assurer qu’aucun client Azure n’est exposé à ces vulnérabilités.

Avec la divulgation publique de la vulnérabilité de sécurité, nous [avons accéléré la maintenance planifiée](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) et avons commencé à redémarrer automatiquement les machines virtuelles qui devaient encore être mises à jour.


## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>Comment puis-je savoir quelles sont les machines virtuelles mises à jour parmi celles que je possède ? 

Vous pouvez voir l’état de vos machines virtuelles, et si leur redémarrage est terminé, en consultant la liste des machines virtuelles [ dans le portail Azure](https://aka.ms/T08tdc). Il est indiqué « Mise à jour déjà effectuée » si la mise à jour a été appliquée, ou « Planifiée » si la mise à jour est encore requise. Si vous souhaitez simplement voir les machines virtuelles dont l’état est « Planifiée », reportez-vous à [Azure Service Health](https://portal.azure.com/).

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>Puis-je savoir exactement quand aura lieu le redémarrage de mes machines virtuelles ?

La meilleure façon d’obtenir une alerte concernant un redémarrage consiste à configurer [Événements planifiés](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events). Vous recevrez une notification 15 minutes avant l’arrêt de la machine virtuelle pour maintenance.

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>Puis-je effectuer le redéploiement manuellement maintenant pour procéder à la maintenance requise ? 

Nous ne pouvons pas garantir qu’une machine virtuelle redéployée sera allouée à un hôte mis à jour. Dans la mesure du possible, la structure Azure tentera d’allouer les machines virtuelles à des hôtes qui sont déjà mis à jour. Il est possible que le redéploiement d’une machine virtuelle se retrouve sur un hôte non mis à jour, auquel cas vous devrez peut-être subir un deuxième redémarrage, cette fois forcé dans le cadre de la maintenance planifiée. Par conséquent, les redéploiements manuels ne sont pas recommandés pour résoudre ce problème.

## <a name="how-long-will-the-reboot-take"></a>Combien de temps va durer le redémarrage ? 

Les redémarrages prennent généralement environ **30 minutes**.

## <a name="does-the-guest-os-need-to-be-updated"></a>Le système d’exploitation invité doit-il être mis à jour ? 

Cette mise à jour de l’infrastructure Azure élimine la vulnérabilité révélée au niveau de l’hyperviseur et ne nécessite pas de mettre à jour vos images de machines virtuelles Windows ou Linux. Comme toujours, vous devez néanmoins continuer à appliquer les meilleures pratiques en matière de sécurité pour vos images de machines virtuelles. Veuillez consulter le fournisseur de vos systèmes d’exploitation concernant les mises à jour et pour obtenir des instructions, le cas échéant. Pour les clients de machines virtuelles Windows Server, des conseils ont maintenant été publiés et sont disponibles [ici](../articles/virtual-machines/windows/mitigate-se.md).

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>La résolution de ce problème via une mise à jour aura-t-elle un impact sur les performances ?

La mise à jour n’a eu aucun impact important sur les performances pour la majorité des clients Azure. Nous nous sommes efforcés d’optimiser l’UC et le chemin d’accès des E/S de disque et n’observons aucun impact significatif sur les performances une fois le correctif appliqué. Il se peut qu’un petit ensemble de clients observe un impact sur les performances réseau. Ce désagrément peut être résolu en utilisant Azure Accelerated Networking pour [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) ou [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli), qui est une fonctionnalité gratuite disponible pour tous les clients Azure.

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>Je suis vos recommandations en matière de haute disponibilité. Mon environnement restera-t-il hautement disponible pendant le redémarrage ?

Oui, les machines virtuelles déployées dans un groupe à haute disponibilité ou dans des groupes de machines virtuelles identiques incluent la construction de domaines de mise à jour (UD). Lors de la réalisation de la maintenance, Azure respecte la contrainte de domaine de mise à jour et ne redémarre pas les machines virtuelles à partir de différents domaines de mise à jour (dans le même groupe à haute disponibilité). Pour plus d’informations sur la haute disponibilité, consultez l’article [Gestion de la disponibilité des machines virtuelles Windows dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) ou [Gestion de la disponibilité des machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>J’ai conçu le plan de continuité des activités/récupération d’urgence de mon entreprise à l’aide de paires de régions. Les redémarrages de mes machines virtuelles se produisent-ils dans les paires de régions en même temps ?

Normalement, les événements de maintenance planifiés Azure sont déployés de manière consécutive dans les régions associées pour réduire le risque d’interruption dans les deux régions. Toutefois, en raison du caractère urgent de cette mise à jour de sécurité, nous déployons la mise à jour dans toutes les régions simultanément.

## <a name="what-about-paas-services-on-azure"></a>Que se passe-t-il pour les services PaaS dans Azure ?  

Les services de la plateforme Azure, notamment les services web, mobiles, de données, IoT, sans serveur, etc., ont tenu compte de la vulnérabilité. Aucune action n’est requise de la part des clients utilisant ces services.

## <a name="intel-released-additional-guidance-on-january-22-2018-related-to-the-security-vulnerabilities--will-this-guidance-cause-any-additional-maintenance-activities-by-azure"></a>Le 22 janvier 2018, Intel a publié des conseils supplémentaires concernant les vulnérabilités en matière de sécurité.  Ces conseils entraîneront-ils des activités supplémentaires de maintenance de la part d’Azure ?  

Les solutions d’atténuation des risques Azure présentées le 3 janvier 2018 ne sont pas affectées par la [mise à jour des conseils](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/) d’Intel. Aucune activité de maintenance supplémentaire n’est prévue sur les machines virtuelles des clients à la suite de ces nouvelles informations.
 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) (Protéger les clients Azure des vulnérabilités de processeur).
