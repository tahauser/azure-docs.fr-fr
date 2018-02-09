> [!NOTE]
> Avec Azure DNS, vous pouvez configurer un nom DNS personnalisé pour vos applications web Azure. Pour plus d’informations, consultez [Use Azure DNS to provide custom domain settings for an Azure service](../articles/dns/dns-custom-domain.md#app-service-web-apps) (Utiliser DNS Azure pour fournir des paramètres de domaine personnalisé pour un service Azure).
>
>

Connectez-vous au site web de votre fournisseur de domaine.

Trouvez la page de gestion des enregistrements DNS. Chaque fournisseur de domaine ayant sa propre interface d’enregistrements DNS, consultez la documentation de votre fournisseur. Recherchez les zones du site qui portent les mentions **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**. 

Vous trouvez généralement la page des enregistrements DNS en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**. Accédez à cette page et recherchez un lien nommé **Fichier de zone**, **Enregistrements DNS**, ou **Configuration avancée**.

La capture d’écran suivante est un exemple d’une page d’enregistrements DNS :

![Exemple de page d’enregistrements DNS](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

Dans l’exemple de capture d’écran, vous cliquez sur **Ajouter** pour créer un enregistrement. Certains fournisseurs ont différents liens pour ajouter divers types d’enregistrements. Là encore, consultez la documentation du fournisseur.

> [!NOTE]
> Pour certains fournisseurs tels que GoDaddy, les modifications apportées aux enregistrements DNS n’entrent en vigueur que lorsque vous cliquez sur un lien **Enregistrer les modifications** distinct. 
