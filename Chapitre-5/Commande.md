# Chapitre 5 : LDAP (Commandes)

### Slide 3/17 (Exercices) : Aperçu de la configuration et de l'annuaire
* `ldapsearch -LLL -Y EXTERNAL -H ldapi:/// -b "cn=config"` : Commande à lancer en root pour obtenir un aperçu complet de la configuration du serveur OpenLDAP.
* `ldapsearch -LLL -Y EXTERNAL -H ldapi:/// -b "dc=esigoto, dc=info"` : Commande à lancer en root pour afficher l'ensemble du contenu de l'annuaire sous le nœud racine.
* `ldapsearch -LLL -D "cn=admin, dc=esigoto,dc=info" -b "dc=esigoto, dc=info" -x -W` : Effectue la même recherche globale dans l'annuaire, mais en s'authentifiant en tant qu'administrateur (le système demande alors le mot de passe défini à l'installation).

### Slide 3/17 & 4/17 (Exercices) : Gestion des schémas et des services
* `vim /etc/ldap/schema/inetorgperson.schema` : Permet de consulter la structure et les attributs du schéma système `inetOrgPerson`.
* `ldapsearch -LLL -Y EXTERNAL -H ldapi:/// -b "cn=schema, cn=config"` : Liste l'ensemble des schémas actuellement connus et activés dans l'annuaire.
* `ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/ldap/schema/inetorgperson.ldif` : Commande permettant d'ajouter ou d'activer un nouveau schéma dans l'annuaire à partir d'un fichier LDIF.
* `service slapd start` : Commande système pour démarrer manuellement le démon OpenLDAP.

### Slide 4/17 (Exercices) : Configuration des logs à chaud
* `ldapsearch -LLL -Y EXTERNAL -H ldapi:/// -b "cn=config" "(olcLogLevel=*)"` : Permet de vérifier la valeur actuelle du paramètre de verbosité des logs du serveur (affiche "none" par défaut).
* `ldapadd -Y EXTERNAL -H ldapi:/// -f config.ldif` : Soumet un fichier LDIF (contenant par exemple une modification `changetype: modify` sur `olcLogLevel`) pour modifier dynamiquement le niveau de log du serveur au runtime.

### Slide 10/17 & 11/17 (Exercices) : Exercices pratiques d'insertion (Schéma existant)
* `ldapadd -D "cn=admin,dc=esigoto,dc=info" -w -f /tmp/racine.ldif` : Ajoute une nouvelle unité organisationnelle (comme `ou=student`) à la racine de l'annuaire.
  * `-D` : Précise le Distinguished Name (DN) du compte administrateur (*rootdn*) pour l'authentification.
  * `-w` : Permet de passer directement le mot de passe en clair (ex: `-w secret`).
* `ldapadd -D "cn=admin, $dc=esi$, dc=be" -w -x -v -f /tmp/ajout.ldif` : Commande verbeuse pour insérer des entrées d'utilisateurs (comme l'objet `inetOrgPerson` pour Juste LEBLANC) définies dans un fichier LDIF.
  * `-x` : Force l'utilisation d'une authentification simple.
  * `-f` : Renseigne le fichier LDIF source (si omis, LDAP attend la saisie sur l'entrée standard *stdin*).
  * `-v` : Active le mode verbeux (*verbose*) pour suivre le détail de l'insertion.

### Slide 11/17 & 12/17 (Exercices) : Recherches avancées et filtres
* `ldapsearch -LLL -x -b "dc=esigoto,dc=info"` : Effectue une recherche globale de type "montre-moi tout" sur l'annuaire spécifié.
* `ldapsearch -LLL -x -b "dc=esigoto,dc=info" "(objectclass=*)" dn sn` : Effectue la même recherche globale mais filtre l'affichage pour ne retourner que les attributs `dn` et `sn` de chaque entrée.

### Slide 14/17 (Exercices) : Schémas personnalisés
* `cat /etc/ldap/schema/core.schema` : Permet d'extraire et de consulter les définitions de schémas de base (comme `person` ou `organizationalUnit`).

### Slide 18/33 : Modèle fonctionnel - Opérations
* Les commandes utilitaires commencent toutes par `ldap_foo_` et correspondent aux opérations du protocole (ex: `ldapsearch`, `ldapcompare`, `ldapadd`, `ldapdelete`, `ldapmodify`, `ldapmodrdn`).

### Slide 19/33 : Modèle fonctionnel - ldapsearch (Syntaxe de base)
* `ldapsearch -x -H ldap://localhost \ -b "dc=example,dc=org" \ "(objectclass=inetOrgPerson)" cn mail` : Commande type pour effectuer une recherche ciblée dans l'annuaire.
  * `-x` : Indique une authentification simple.
  * `-H` : Spécifie l'URI du serveur.
  * `-b` : Spécifie la base de recherche (base DN).
  * `"(objectclass=inetOrgPerson)"` : Filtre appliqué à la recherche.
  * `cn mail` : Précise les attributs à retourner.

### Slide 27/33 : OpenLDAP - Implémentation
* `slapd`, `ldap-utils` : Noms des paquets à installer sur la distribution (ex: via `apt install`) pour disposer du serveur et des utilitaires de commande.

### Slide 29/33 : Open LDAP-TLS - Configuration (serveur)
* Pour appliquer la configuration des clés et certificats (`/etc/ldap/tls/ca.crt`, `server.crt`, `server.key`), on injecte un fichier LDIF de modification via la commande d'authentification système externe habituelle.

### Slide 30/33 : OpenLDAP - Configuration LDAPS (port 636)
* `systemctl restart slapd` : Commande système pour redémarrer complètement le service d'annuaire après modification du fichier `/etc/default/slapd`.

### Slide 31/33 : OpenLDAP - Tests connexion sécurisée
* `ldapsearch -ZZ -H ldap://server -x \ -b "dc=example,dc=org" "(objectclass=*)"` : Teste la connexion sécurisée via StartTLS sur le port standard (port 389).
  * `-ZZ` : Argument spécifique pour forcer l'initiation et la négociation immédiate de StartTLS.
* `ldapsearch -H ldaps://server -x \ -b "dc=example,dc=org" "(objectclass=*)"` : Teste la connexion sécurisée via le protocole LDAPS.
  * `ldaps://` : Indique explicitement au client de se connecter sur le port dédié et chiffré par défaut (port 636).

<br>

<p style="display:flex;justify-content:space-between;align-items:center;">
<a href="../Chapitre-4/Commande.md">⬅️ Chapitre précédent</a> | 
<a href="../README.md">Vers le readme ➡️</a>
</p>