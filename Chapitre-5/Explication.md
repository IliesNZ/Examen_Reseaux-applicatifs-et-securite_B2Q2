# Chapitre 5 : LDAP (Théorie)

### Slide 1/17 (Exercices) : Introduction et concepts d'annuaire
* **Définition de LDAP** : Signifie *Lightweight Directory Access Protocol*. Il s'agit d'un protocole d'accès à un annuaire.
* **Nature d'un annuaire** : C'est une base de données spécialisée dotée d'une structure forte, conçue pour répertorier tout ce qui peut être nommé. Un annuaire n'est pas un SGBD classique.
* **Évolution de la configuration OpenLDAP** : Depuis la version 2.4, OpenLDAP n'utilise plus le fichier unique `slapd.conf`. La configuration est elle-même stockée dans un annuaire LDAP spécifique situé dans le répertoire `/etc/ldap/slapd.d` (nœud `cn=config`), ce qui permet des modifications à chaud au runtime. Il est fortement déconseillé de modifier directement ces fichiers textuels LDIF.

### Slide 2/33 & 2/17 (Exercices) : Ce que fournit LDAP
* **Composants du protocole** : LDAP fournit un protocole d'accès, un modèle d'informations, des conventions de nommage, un modèle fonctionnel, de sécurité et de duplication, des API de programmation et le format d'échange LDIF.

### Slide 6/33 & 2/17 (Exercices) : Modèle de données - Structure
* **Structure en arbre** : L'annuaire est organisé sous forme d'arbre d'informations. Chaque nœud de cet arbre représente une entrée (un objet).
* **Racine (RootDSE)** : La racine de l'arbre contient la description complète de l'annuaire. Lors d'une installation standard (ex: sous Debian), le système crée un rootDSE basé sur le nom de domaine de la machine (ex: `dc=esigoto,dc=info`).

### Slide 7/33, 8/33 & 3/17 (Exercices) : Schémas et ObjectIDs (OID)
* **Définition d'un schéma** : Pour fonctionner, un serveur LDAP a besoin de schémas qui définissent les classes d'objets et les attributs utilisables.
* **Structure d'un attribut** : Un attribut possède obligatoirement un nom, un identifiant unique (OID), une syntaxe et un format de valeur.
* **Object Class (Classe d'objet)** : Une classe d'objet possède un nom, un OID, un type (structurel, abstrait ou auxiliaire) et une liste d'attributs obligatoires (`MUST`) ou optionnels (`MAY`).
* **Identifiants OID** : Les ObjectIDs sont normalisés (hiérarchie et héritage). Ils sont approuvés mondialement par l'IANA qui attribue un numéro d'entreprise unique (ex: `23162` pour l'école). On ne peut pas modifier la structure au-dessus de ce numéro, mais on organise librement l'arbre en dessous (ex: dédier un arc aux classes d'objets et un autre aux attributs).
* **Schema Checking** : Lors de l'insertion d'une entrée, le serveur vérifie obligatoirement si sa syntaxe et ses attributs sont conformes aux schémas actifs.

### Slide 10/33 & 10/17 (Exercices) : Classes d'objets standards
* **Classe `person`** : Définie dans `core.schema`, elle exige les attributs `sn` (surname) et `cn` (common name). Elle peut contenir en option `userPassword`, `telephoneNumber`, `seeAlso` et `description`.
* **Classe `inetOrgPerson`** : Définie dans `inetorgperson.schema`, elle est spécifiquement conçue pour stocker des personnes dans un environnement internet/réseau. Elle hérite directement de la classe `organizationalPerson` (qui elle-même hérite de `person`).

### Slide 12/33 : Conventions de nommage - Le DN
* **Distinguished Name (DN)** : Chaque nœud/entrée possède un identifiant unique absolu appelé DN. Il est composé d'une suite d'attributs (ex: `uid=fpignon, ou=construction, dc=example, dc=org`).

### Slide 14/33 & 7/17 (Exercices) : Le format LDIF
* **Définition** : Signifie *LDAP Data Interchange Format*. C'est le format standard de représentation des données textuelles pour l'import, l'export et les modifications dans l'annuaire.
* **Règles de syntaxe strictes** :
  * Les fichiers doivent être encodés en UTF-8 (souvent codés en Base64 pour éviter l'altération des caractères spéciaux).
  * Une ligne commençant par un `#` est un commentaire.
  * Une ligne commençant par un espace continue la ligne précédente.
  * Une ligne vide sépare deux entrées distinctes.
  * Un tiret (`-`) permet de terminer une opération de modification pour en commencer une nouvelle sur la même entrée.

### Slide 9/17 (Exercices) : Types de modifications LDIF (`changetype: modify`)
* **`add`** : Ajoute un attribut à une entrée. Si l'attribut existe déjà, une nouvelle paire attribut/valeur est ajoutée (attribut multi-valué).
* **`delete`** : Supprime toutes les valeurs d'un attribut, sauf si une valeur spécifique est précisée.
* **`replace`** : Remplace toutes les valeurs existantes d'un attribut par la nouvelle valeur spécifiée.

### Slide 5/17 & 6/17 (Exercices) : Les ACL OpenLDAP (Contrôle d'accès)
* **Rôle** : Permettent de définir finement "qui" (all, anonymous, users, self, expression régulière) a accès à "quoi" (un DN, un filtre ou des attributs précis).
* **Niveaux d'accès** : Définis par la directive `olcAccess`. Ils sont hiérarchiques et doivent être choisis dans l'ordre (donner un droit implique de posséder tous les droits inférieurs) : `none` (0), `disclose` (d), `auth` (dx), `compare` (cdx), `search` (scdx), `read` (rscdx), `write` (wrscdx), `manage` (mwrscdx).
* **Importance de l'ordre** : L'ordre des directives `olcAccess` est crucial. Lors de l'insertion, le serveur leur attribue automatiquement un numéro d'ordre entre accolades `{0}`, `{1}`, `{2}`.

### Slide 18/33 : Modèle fonctionnel - Catégories d'opérations
* **Authentification** : Opérations de connexion/déconnexion au serveur (`bind`, `unbind`, `abandon`).
* **Requête** : Opérations d'interrogation (`search` pour la recherche et `compare` pour la vérification de la valeur d'un attribut).
* **Mise à jour** : Opérations de modification des données (`add`, `delete`, `modify` pour le CRUD et `modrdn` pour renommer/déplacer une entrée).

### Slide 20/33 & 12/17 (Exercices) : Portée de la recherche (*Search Scope*)
* **`base`** : La recherche se limite uniquement à l'entrée spécifiée elle-même.
* **`onelevel` / `one`** : La recherche inspecte uniquement les enfants directs du nœud de base.
* **`subtree` / `sub`** : La recherche inspecte l'entrée de base ainsi que toute sa sous-arborescence descendante.

### Slide 22/33 : Securité - Pourquoi sécuriser LDAP ?
* **Vulnérabilité par défaut** : Par défaut, le protocole LDAP transmet toutes les informations en clair sur le réseau. Cela inclut les requêtes de recherche, les données confidentielles retournées, ainsi que les mots de passe lors de la phase d'authentification (`bind`), ce qui constitue une faille de sécurité majeure.

### Slide 23/33 : Méthodes de sécurisation (LDAPS vs StartTLS)
* **LDAPS (Port 636)** : Le chiffrement TLS est initié dès le début de la connexion, de manière similaire à une connexion HTTPS. Nécessite d'activer le protocole `ldaps:///` dans la configuration du démon.
* **StartTLS (Port 389)** : La connexion s'établit d'abord normalement sur le port standard en clair, puis une phase de prénégociation applicative bascule (upgrade) la session en mode chiffré TLS.

<br>

<p style="display:flex;justify-content:space-between;align-items:center;">
<a href="../Chapitre-4/Theorie.md">⬅️ Chapitre précédent</a> | 
<a href="../README.md">Vers le readme ➡️</a>
</p>