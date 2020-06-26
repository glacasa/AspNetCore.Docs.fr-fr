## <a name="forward-request-information-with-a-proxy-or-load-balancer"></a>Transférer les informations sur la demande avec un proxy ou un équilibreur de charge

Si l’application est déployée derrière un serveur proxy ou un équilibreur de charge, certaines informations sur la demande d’origine peuvent être transférées vers l’application dans les en-têtes de demande. Ces informations incluent généralement le schéma de demande sécurisé (`https`), l’hôte et l’adresse IP du client. Les applications ne lisent pas automatiquement ces en-têtes de demande pour découvrir et d’utiliser les informations sur la demande d’origine.

Le schéma est utilisé dans la génération de lien qui affecte le flux d’authentification dans le cas de fournisseurs externes. En cas de perte du schéma sécurisé (`https`), l’application génère des URL de redirection incorrectes et non sécurisées.

Utilisez l’intergiciel Forwarded Headers afin de mettre les informations de demande d’origine à la disposition de l’application pour le traitement des demandes.

Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.
