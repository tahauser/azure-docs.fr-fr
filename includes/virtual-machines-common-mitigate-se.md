


**Dernière mise à jour du document** : 22 janvier, 15:00 PST.

La divulgation récente d’une [nouvelle classe de vulnérabilités de processeur](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) appelées attaques par canal latéral de l’exécution spéculative a généré des questions de la part de clients recherchant plus d’explications.  

L’infrastructure qui exécute Azure et isole les différentes charges de travail des clients est protégée.  Cela signifie que d’autres clients utilisant Azure ne peuvent pas attaquer votre application avec ces vulnérabilités.

> [!NOTE] 
> Les solutions d’atténuation des risques Azure présentées le 3 janvier 2018 ne sont pas affectées par la [mise à jour des conseils](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/) récente d’Intel. Aucune activité de maintenance supplémentaire n’est prévue sur les machines virtuelles des clients à la suite de ces nouvelles informations.
>
> Nous continuerons à mettre à jour ces bonnes pratiques à mesure que nous recevrons des mises à jour du microcode des fournisseurs de matériel. Consultez de nouveau la mise à jour des conseils.
>

## <a name="keeping-your-operating-systems-up-to-date"></a>Maintien à jour de vos systèmes d’exploitation

Alors qu’une mise à jour du système d’exploitation n’est pas nécessaire pour isoler vos applications s’exécutant sur Azure des autres clients utilisant Azure, il est toujours judicieux de maintenir vos versions de système d’exploitation à jour. 

Vous trouverez ci-dessous nos actions recommandées pour mettre à jour votre système d’exploitation en fonction des offres suivantes : 

<table>
<tr>
<th>Offre</th> <th>Action recommandée </th>
</tr>
<tr>
<td>Services cloud Azure </td>  <td>Activez la mise à jour automatique ou vérifiez que vous exécutez le dernier système d’exploitation invité.</td>
</tr>
<tr>
<td>Machines virtuelles Linux Azure</td> <td>Installez les mises à jour de votre fournisseur de système d’exploitation dès qu’elles sont disponibles. </td>
</tr>
<tr>
<td>Machines virtuelles Windows Azure </td> <td>Vérifiez que vous exécutez une application antivirus prise en charge avant d’installer les mises à jour du système d’exploitation. Contactez votre fournisseur de logiciel antivirus pour obtenir des informations sur la compatibilité.<p> Installez le [correctif cumulatif de sécurité de janvier](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002). </p></td>
</tr>
<tr>
<td>Autres services PaaS Azure</td> <td>Aucune action n’est requise de la part des clients utilisant ces services. Azure maintient automatiquement à jour les versions du système d’exploitation. </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Conseils supplémentaires si vous exécutez du code non approuvé 

Aucune action supplémentaire n’est requise de la part du client, sauf si vous exécutez du code non approuvé. Si vous autorisez l’utilisation de code non approuvé (par exemple, vous autorisez l’un de vos clients à charger un extrait de code ou un fichier binaire que vous exécutez ensuite dans le cloud au sein de votre application), vous devez suivre les étapes supplémentaires suivantes.  


### <a name="windows"></a>Windows 
Si vous utilisez Windows et hébergez du code non approuvé, vous devez également activer une fonctionnalité Windows appelée Kernel Virtual Address (KVA) Shadowing (Copie shadow d’adresse virtuelle du noyau), qui fournit une protection supplémentaire contre les vulnérabilités par canal latéral de l’exécution spéculative. Par défaut, cette fonctionnalité est désactivée ; en cas d’activation, elle peut avoir des conséquences sur les performances. Suivez les instructions de [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) pour l’activation des protections sur le serveur. Si vous exécutez Azure Cloud Services, vérifiez que vous utilisez WA-GUEST-OS-5.15_201801-01 ou WA-GUEST-OS-4.50_201801-01 (disponible à partir du 10 janvier) et activez la clé de Registre via une tâche de démarrage.


### <a name="linux"></a>Linux
Si vous utilisez Linux et hébergez du code non approuvé, vous devez également mettre à jour Linux vers une version plus récente qui implémente KPTI (Kernel Page Table Isolation, isolation de tables de pages du noyau) séparant les tables de pages utilisées par le noyau de celles appartenant à l’espace utilisateur. Ces solutions d’atténuation nécessitent une mise à jour du système d’exploitation Linux et peuvent être obtenues auprès de votre fournisseur de solutions de distribution dès qu’elles sont disponibles. Votre fournisseur de système d’exploitation peut vous indiquer si les protections sont activées ou désactivées par défaut.



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) (Protéger les clients Azure des vulnérabilités de processeur).
