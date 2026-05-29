# Chapitre 2 : IDS|IPS (Commandes)

## Analyse des logs (Slide 8) :

### Expression régulière (pattern) d'exemple pour l'analyse des logs :

- logcheck (lancé toutes les heures) 
- logwatch (lancé une fois par jour) 

lisent les fichiers de logs et envoient des alertes dès qu'une ligne de log correspond à un
pattern.

    ^\w{3} [ :0-9]{11} [.[:alnum:]-]+ SSMTP\[[0-9]+\]: Sent mail\ for . $*\backslash([\Theta-9]+[\Theta-9.]+$ Bye\) uid=[0-9]+ username=[\._[:alnum:]\ -]+ outbytes=[0-9]+\$   

## Fail2ban (Slide 10-12)

### Fichier de configuration :

- Configurations globales : /etc/fail2ban/{fail2ban.conf,jail.conf,filter.d,action.d}   
- Configuration locale (à privilégier pour l'écriture) : /etc/fail2ban/jail.local   

### Exemple de bloc de configuration :

    Ini, TOML

    [DEFAULT] 
    bantime = <long enough> 
    findtime = <time during retry are counted> 
    maxretry = <n> 
    action = %(action_mw)s 

    [<service>] 
    enabled = true 

<b>recidive</b> : incrémente le bantime d'une IP
récidiviste.


### Commandes client & Firewall

#### Client

    fail2ban-client status : Affiche le statut général.  

    fail2ban-client status <JAIL> : Affiche le statut d'une prison spécifique.  
    
    fail2ban-client set loglevel <LEVEL> : Modifie le niveau de log.  
    
    fail2ban-client get loglevel : Affiche le niveau de log actuel.  
    
    fail2ban-client set <JAIL> [un]banip <IP> : Bannit ou débannit manuellement une IP dans une prison. 
    
    fail2ban-client set <JAIL> <add|del> ignoreip <IP> : Ajoute ou retire une IP de la liste d'ignorance d'une prison.  

#### Firewall
    
    iptables -L : Montre toutes les règles du firewall en cours (utile pour vérifier les actions de fail2ban).  

## Tripwire (Slide 14-20) :

### Intro 

    $sha512sum /etc/hosts : Commande d'exemple pour calculer le hash d'un fichier.  

### Génération des clés
    
    twadmin --generate-keys -L /etc/tripwire/<host>-local.key : Génère la clé locale.  
    
    twadmin --generate-keys -L /etc/tripwire/site.key : Génère la clé de site.  
    
### Fichiers sources

- /etc/tripwire/twcfg.txt : Fichier source pour générer la BD chiffrée de configuration (tw.cfg).  

- /etc/tripwire/twpol.txt : Fichier source pour générer la BD chiffrée des règles (tw.pol).  
    
### Création des bases chiffrées

    twadmin --create-cfgfile -S /etc/tripwire/site.key \ /etc/tripwire/twcfg.txt : Crée le fichier de configuration chiffré.  

    twadmin --create-polfile -S /etc/tripwire/site.key \ /etc/tripwire/twpol.txt : Crée le fichier de politique chiffré.  

### Configuration des règles à surveiller :

    Plaintext
    /etc -> $(SEC_BIN); 
    /etc/passwd -> $(SEC_CONFIG); 
    /etc/shadow -> $(SEC_CONFIG); 

### Initialisation

    tripwire --init : Initialise la base de données des fichiers surveillés.  
    
- /var/lib/tripwire/<host>.twd : Emplacement où le fichier de base de données est créé.  

### Vérification et lecture des rapports

    tripwire --check : Lance une vérification d'intégrité. 

    twprint -m r --twrfile /var/lib/tripwire/report/<host>-<date>.twr : Commande pour lire le rapport généré en clair.  

- /var/lib/tripwire/report : Dossier contenant les rapports générés au format .twr.  


## Portsentry (Slide 25) :

### Dans le fichier /etc/portsentry/portsentry.conf:  
    
    BLOCK_UDP="1"   
    BLOCK_TCP="1"   
    KILL_RUN_CMD=/sbin/iptables   

### Dans le fichier /etc/default/portsentry:  
    
    TCP_MODE="atcp"   
    UDP_MODE="audp"   

<br>

<p style="display:flex;justify-content:space-between;align-items:center;">
<a href="../Chapitre-1/Commande.md">⬅️ Chapitre Précédent</a> | 
<a href="../Chapitre-3/Commande.md">Chapitre suivant ➡️</a>
</p>