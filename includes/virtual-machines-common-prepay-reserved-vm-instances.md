# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>Prépayer les machines virtuelles avec des instances de machines virtuelles réservées

Prépayez les machines virtuelles et réalisez des économies avec les instances de machines virtuelles réservées. Pour plus d’informations, consultez [Offre des instances de machines virtuelles réservées](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Vous pouvez acheter des instances de machines virtuelles réservées sur le [portail Azure](https://portal.azure.com). Pour acheter une instance de machine virtuelle réservée :
-   Vous devez avoir un rôle de propriétaire pour au moins un abonnement Entreprise ou Paiement à l’utilisation.
-   Pour les abonnements Entreprise, les achats de réservation doivent être activés dans le [portal EA](https://ea.azure.com).

## <a name="buy-a-reserved-virtual-machine-instance"></a>Acheter une instance de machine virtuelle réservée
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez **Ajouter** pour acheter une nouvelle réservation.
4. Renseignez les champs obligatoires. Les instances de machines virtuelles en cours d’exécution qui correspondent aux attributs que vous sélectionnez se qualifient pour bénéficier de la remise de réservation. Le nombre réel de vos instances de machines virtuelles qui obtiennent la remise dépend de l’étendue et de la quantité sélectionnées.

    | Champ      | Description|
    |:------------|:--------------|
    |NOM        |Nom de cette réservation.| 
    |Abonnement|Abonnement utilisé pour payer la réservation. Les coûts initiaux de la réservation sont facturés au mode de paiement défini sur l’abonnement. Le type d’abonnement doit être un contrat Entreprise (numéro de l’offre : MS-AZR-0017P) ou Paiement à l’utilisation (numéro de l’offre : MS-AZR-0003P). Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. Pour un abonnement Paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture défini sur l’abonnement.|    
    |Étendue       |L’étendue de la réservation peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : <ul><li>Abonnement unique : la remise de réservation est appliquée aux machines virtuelles incluses dans cet abonnement. </li><li>Partagé : la remise de réservation est appliquée aux machines virtuelles en cours d’exécution dans tous les abonnements au sein de votre contexte de facturation. Pour les clients Entreprise, l’étendue partagée correspond à l’inscription et inclut tous les abonnements (à l’exception des abonnements de développement/test) au sein de l’inscription. Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.</li></ul>|
    |Lieu    |Région Azure couverte par la réservation.|    
    |Taille de la machine virtuelle     |Taille des instances de machines virtuelles.|
    |Terme        |Une année ou trois ans.|
    |Quantité    |Nombre d’instances achetées au sein de la réservation. La quantité correspond au nombre d’instances de machines virtuelles en cours d’exécution pouvant bénéficier de la remise de facturation. Par exemple, si vous exécutez 10 machines virtuelles Standard_D2 dans la région Est des États-Unis, vous devez spécifier 10 comme quantité pour optimiser l’avantage pour toutes les machines en cours d’exécution. |
5. Vous pouvez afficher le coût de la réservation lorsque vous sélectionnez **Calculer le coût**.

    ![Capture d’écran avant de soumettre l’achat de la réservation](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Sélectionnez **Achat**.
7. Sélectionnez **Afficher cette réservation** pour connaître l’état de votre achat.

    ![Capture d’écran avant de soumettre l’achat de la réservation](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Étapes suivantes 
La remise de réservation est appliquée automatiquement au nombre de machines virtuelles en cours d’exécution qui correspondent à l’étendue et aux attributs de la réservation. Vous pouvez mettre à jour l’étendue de la réservation par le biais du [portail Azure](https://portal.azure.com), de PowerShell, de CLI ou de l’API. 

Pour savoir comment gérer une réservation, consultez [Gérer Azure Reserved Virtual Machine Instances](../articles/billing/billing-manage-reserved-vm-instance.md).

Pour plus d’informations sur les instances de machine virtuelle réservées, consultez les articles suivants.

- [Réaliser des économies sur les machines virtuelles avec les instances de machine virtuelle réservées](../articles/billing/billing-save-compute-costs-reservations.md)
- [Comprendre comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation de l’offre d’instance réservée pour l’inscription de votre entreprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](../articles/billing/billing-reserved-instance-windows-software-costs.md)