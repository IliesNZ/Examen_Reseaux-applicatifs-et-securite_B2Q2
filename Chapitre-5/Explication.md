# Chapitre 5 : LDAP

## Introduction à LDAP
* **Définition** : LDAP (Lightweight Directory Access Protocol) est un protocole d'accès à un annuaire.
* **Concept** : Un annuaire n'est pas un système de gestion de base de données (SGBD) classique. Il s'agit d'une base de données spécialisée dotée d'une structure forte , conçue pour répertorier tout ce qui peut être nommé[cite: 9].
* **Ce que LDAP fournit** : Un protocole d'accès, un modèle d'informations, des conventions de nommage, un modèle fonctionnel, de sécurité et de duplication, une API, et le format LDIF.

## Modèle de Données
* **Structure** : Les données sont organisées sous la forme d'une structure en arbre[cite: 25].
* **Nœuds** : Chaque nœud de cet arbre représente une entrée ou un objet. La racine contient la description de l'arbre, appelée `rootDSE`.
* **Identifiant Unique (DN)** : Chaque nœud possède un identifiant unique (Distinguished Name) composé des attributs obligatoires. Un exemple de DN est `uid=fpignon, ou=construction, dc=example,dc=org`.
* **Classes d'objets (`objectClass`)** : Elles sont définies par un nom, un `objectID`, des attributs obligatoires (MUST) et optionnels (MAY), ainsi qu'un type (structurel, abstrait ou auxiliaire).
* **Attributs** : Un attribut possède un nom, un OID, une syntaxe et un format de valeur[cite: 39].
* **ObjectID** : Les OID sont normalisés (par exemple, via RFC2256) et fonctionnent par un système de hiérarchie et d'héritage[cite: 44, 45].
* **Schema Checking** : Lors de l'insertion d'une nouvelle entrée, le serveur effectue une vérification pour s'assurer que la syntaxe est bien conforme au schéma.

## Le format LDIF
* **Définition** : LDIF signifie LDAP Data Interchange Format[cite: 93, 94].
* **Utilité** : C'est le format utilisé pour la représentation des données, que ce soit pour des opérations d'importation, d'exportation ou pour y apporter des modifications (ajouts, suppressions).

## Modèle Fonctionnel et Requêtes
* **Opérations définies par le protocole** :
    * **Authentification** : `bind` (connexion), `unbind` (déconnexion), `abandon`, et connexion au serveur[cite: 126].
    * **Requête** : `search` pour la recherche et `compare` pour la comparaison d'un attribut.
    * **Mise à jour** : `add`, `delete`, `modify` pour le CRUD (Create, Read, Update, Delete) et `modrdn` pour la modification dans un shell[cite: 126].
* **L'outil `ldapsearch`** : Il permet d'effectuer des recherches dans l'annuaire[cite: 132]. Les commandes courantes utilisent `-x` (authentification simple), `-H` (URI du serveur), `-b` (base de recherche), ainsi qu'un filtre et des attributs à retourner.
* **Portée de la recherche (Search Scope)** : La recherche peut se limiter à l'entrée elle-même (`base`), aux enfants directs (`onelevel`), ou s'étendre à toute la sous-arborescence (`subtree`).

## Sécurité (LDAP et TLS)
* **La faille par défaut** : Par défaut, LDAP transmet toutes les données en clair, y compris les requêtes de recherche, les données retournées et les mots de passe lors du bind[cite: 158, 160, 161, 162]. Cela représente une faille de sécurité majeure[cite: 163].
* **LDAPS (Port 636)** : Permet d'initier le chiffrement TLS dès la connexion[cite: 168]. Son activation nécessite une configuration TLS préalable avec des certificats (CA, serveur, clé) et la modification du fichier `/etc/default/slapd`[cite: 206, 207, 220, 226].
* **StartTLS (Port 389)** : Permet une prénégociation pour "upgrade" une connexion standard vers une connexion TLS[cite: 168]. Cela peut être testé avec l'option `-ZZ` via la commande `ldapsearch`.

## Implémentations et Architecture
* **Serveurs** : Les solutions incluent OpenLDAP, Microsoft Active Directory et Netscape Directory Server.
* **Clients** : Browsers, MS Windows, PAM LDAP, les API (Perl, Java, etc.) et Samba.
* **OpenLDAP** : Il s'agit d'une implémentation libre de LDAP[cite: 190]. Elle comprend les paquets `slapd` et `ldap-utils`, ainsi que les dæmons `slapd` et `slurpd`[cite: 192, 193]. Sa configuration demande le choix de schémas, la gestion de `/etc/ldap/slapd.d` et l'usage des utilitaires `ldapfoo`.

<br>

<p style="display:flex;justify-content:space-between;align-items:center;">
<a href="../Chapitre-4/Explication.md">⬅️ Chapitre précédent</a> | 
<a href="../README.md">Vers le readme ➡️</a>
</p>