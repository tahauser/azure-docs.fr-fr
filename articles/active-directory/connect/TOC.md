# Vue d'ensemble
## [Qu’est-ce qu’Azure AD Connect ?](active-directory-aadconnect.md)
## [Qu’est-ce que la synchronisation d’Azure AD Connect ?](active-directory-aadconnectsync-whatis.md)
### [Les utilisateurs et les contacts](active-directory-aadconnectsync-understanding-users-and-contacts.md)
### [Architecture](active-directory-aadconnectsync-understanding-architecture.md)
### [Approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
#### [Explique les expressions d’approvisionnement déclaratif.](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
### [Configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md)
## [Qu’est-ce que la fédération avec Azure AD Connect ?](active-directory-aadconnectfed-whatis.md)


# Prise en main
## [Composants requis](active-directory-aadconnect-prerequisites.md)
## [Installer Azure AD Connect](active-directory-aadconnect-select-installation.md)
### [Paramètres Express](active-directory-aadconnect-get-started-express.md)
### [Paramètres personnalisés](active-directory-aadconnect-get-started-custom.md)
### [Mise à niveau à partir de DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
### [Mettre à niveau à partir d’une version antérieure](active-directory-aadconnect-upgrade-previous-version.md)
### [Effectuer l’installation à l’aide d’une base de données ADSync existante](active-directory-aadconnect-existing-database.md)
### [Effectuer l’installation à l’aide d’autorisations administrateur déléguées SQL](active-directory-aadconnect-sql-delegation.md)

# Procédures
## Planifier et concevoir
### [Principes de conception](active-directory-aadconnect-design-concepts.md)
### [Topologies pour Azure AD Connect](active-directory-aadconnect-topologies.md)
### [Active Directory Federation Services dans Azure](active-directory-aadconnect-azure-adfs.md)
### [Considérations spécifiques concernant les instances](active-directory-aadconnect-instances.md)
### [Si vous disposez déjà d’Azure AD](active-directory-aadconnect-existing-tenant.md)
## [Gestion d’Azure AD Connect](active-directory-aadconnect-whats-next.md)
### [Renouveler les certificats pour O365 et Azure AD](active-directory-aadconnect-o365-certs.md)
### [Mettre à jour le certificat SSL pour une batterie de serveurs Active Directory Federation Services (AD FS)](active-directory-aadconnectfed-ssl-update.md)
### [Activer l’écriture différée des appareils](active-directory-aadconnect-feature-device-writeback.md)
### [Options d’authentification unique de l’utilisateur](active-directory-aadconnect-user-signin.md)
#### [Authentification unique transparente](active-directory-aadconnect-sso.md)
##### [Démarrage rapide](active-directory-aadconnect-sso-quick-start.md)
##### [Comment cela fonctionne-t-il ?](active-directory-aadconnect-sso-how-it-works.md)
##### [Forum Aux Questions](active-directory-aadconnect-sso-faq.md)
##### [Résolution des problèmes](active-directory-aadconnect-troubleshoot-sso.md)
##### [RGPD](active-directory-aadconnect-sso-gdpr.md)
#### [Authentification directe](active-directory-aadconnect-pass-through-authentication.md)
##### [Démarrage rapide](active-directory-aadconnect-pass-through-authentication-quick-start.md)
##### [Limitations actuelles](active-directory-aadconnect-pass-through-authentication-current-limitations.md)
##### [Comment cela fonctionne-t-il ?](active-directory-aadconnect-pass-through-authentication-how-it-works.md)
##### [Mise à niveau des agents d’évaluation](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md)
##### [Verrouillage intelligent](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)
##### [Forum Aux Questions](active-directory-aadconnect-pass-through-authentication-faq.md)
##### [Résolution des problèmes](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)
##### [Présentation approfondie de Sécurité](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)
##### [RGPD](active-directory-aadconnect-pass-through-authentication-gdpr.md)
### [Prise en charge de plusieurs domaines pour la fédération](active-directory-aadconnect-multiple-domains.md)
### [Mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md)
### [Utiliser un fournisseur d’identité (IdP) SAML 2.0 pour l’authentification unique](active-directory-aadconnect-federation-saml-idp.md)



## Gérer la synchronisation d’Azure AD Connect
### [Conformité avec RGPD et Azure AD Connect](active-directory-aadconnect-gdpr.md)
### [prévention des suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
### [Synchronisation du mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md)
### [Compte de service Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md)
### [Assistant d’installation](active-directory-aadconnectsync-installation-wizard.md)
### [Mode de remplissage de UserPrincipalName](active-directory-aadconnect-userprincipalname.md)
### [Modifier la configuration par défaut](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)
### [Configurer le filtrage](active-directory-aadconnectsync-configure-filtering.md)
### [Scheduler](active-directory-aadconnectsync-feature-scheduler.md)
### [Extensions d’annuaire](active-directory-aadconnectsync-feature-directory-extensions.md)

### [Modifier le mot de passe du compte de service Azure AD Sync](active-directory-aadconnectsync-change-serviceacct-pass.md)
### [Modifier le mot de passe du compte AD DS](active-directory-aadconnectsync-change-addsacct-pass.md)
### [Activer la Corbeille Active Directory](active-directory-aadconnectsync-recycle-bin.md)

### [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)
#### [Opérations](active-directory-aadconnectsync-service-manager-ui-operations.md)
#### [Connecteurs](active-directory-aadconnectsync-service-manager-ui-connectors.md)
#### [Metaverse designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)
#### [Recherche de métaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)


## Gérer les services de fédération
### [Gérer et personnaliser](active-directory-aadconnect-federation-management.md)
### [Fédérer plusieurs instances d’Azure AD avec une seule instance AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)


## Résolution des problèmes
### [Connectivité](active-directory-aadconnect-troubleshoot-connectivity.md)
### [Résolution des erreurs lors de la synchronisation](active-directory-aadconnect-troubleshoot-sync-errors.md)
### [Objet non synchronisé](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)
### [Synchronisation d’objets à l’aide de la tâche de résolution des problèmes](active-directory-aadconnect-troubleshoot-objectsync.md)
### [Synchronisation du mot de passe](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)
### [Erreur de LargeObject provoquée par userCertificate](active-directory-aadconnectsync-largeobjecterror-usercertificate.md)
### [Comment récupérer depuis la limite de 10 Go de base de données locale](active-directory-aadconnect-recover-from-localdb-10gb-limit.md)

# Informations de référence
## [Exemples de code](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
## [Synchronisation des identités et résilience d’attribut en double](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
## [Ports et protocoles nécessaires à l’identité hybride](active-directory-aadconnect-ports.md)
## [Fonctionnalités de la version préliminaire](active-directory-aadconnect-feature-preview.md)
## [Historique des versions](active-directory-aadconnect-version-history.md)
## [Comptes et autorisations](active-directory-aadconnect-accounts-permissions.md)

## Synchronisation d’Azure AD Connect
### [Attributs synchronisés avec Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md)
### [Historique de publication des versions du connecteur](active-directory-aadconnectsync-connector-version-history.md)
### [Référence des fonctions](active-directory-aadconnectsync-functions-reference.md)
### [Considérations et tâches opérationnelles](active-directory-aadconnectsync-operations.md)
### [Liste de compatibilité de fédération Azure AD](active-directory-aadconnect-federation-compatibility.md)
### [concepts techniques](active-directory-aadconnectsync-technical-concepts.md)
### [Fonctionnalités du service](active-directory-aadconnectsyncservice-features.md)




# Rubriques connexes
## [Surveillez votre infrastructure d’identité locale et vos services de synchronisation dans le cloud](../connect-health/active-directory-aadconnect-health.md)
## [Guide sur la conception d’identités hybrides](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)


# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
##[FAQ Azure AD Connect](active-directory-aadconnect-faq.md)
##[Abandon de la synchronisation de répertoires](active-directory-aadconnect-dirsync-deprecated.md)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)

