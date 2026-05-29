# Chapitre 1 : Première connexion (Commandes)

## MDP (Slide 3) :
    
    pwgen : Commande suggérée pour générer un mot de passe complexe.  

## Connexion (Slide 4) :
    ClientAliveInterval 300 : Fixe le wakeup (timeout) de la connexion.  

    ClientAliveCountMax 3 : Fixe le nombre de rappels avant que l'utilisateur ne soit déconnecté.  

    PermitRootLogin no : N'autorise pas de connexion directe en root.  
    
    AllowUsers <your users> : Précise le nom des comptes autorisés à se connecter.  

## Sudo (Slide 5) :

    %sudo ALL=(ALL:ALL) ALL : Ligne permettant d'ajouter l'utilisateur au groupe sudo.  

    Defaults timestamp_timeout=0 : Configuration supplémentaire pour gérer le timeout de sudo.  

## Timeout SSH (Slide 6):

### Fichier cible : /etc/profile.d/timeout.sh   
#### Lignes à y ajouter :

        echo "Idle users will be removed after 15 minutes"   
        TMOUT=900
        readonly TMOUT      # Empêche un utilisateur de modifier la variable
        export TMOUT        # Exporte la variable dans l'environnement de l'utilisateur

## Installation (Slide 7) :

    apt : Outil pour la gestion des paquets.  

    Fichier cible : sources.list (Il est recommandé d'y préciser le nom d'une version comme trixie plutôt que stable ).  

    ntp : Exemple de paquet à installer pour disposer d'un serveur de temps.  

## Mail root (Slide 8) :

    Fichier cible : /etc/aliases   

    Ligne à y ajouter (pour que le serveur puisse envoyer les alertes root) :
        root: me@example.org

<br>

<p style="display:flex;justify-content:space-between;align-items:center;">
<a href="../README.md">⬅️ Vers le readme</a> | 
<a href="../Chapitre-2/Commande.md">Chapitre suivant ➡️</a>
</p>
