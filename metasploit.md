# Metasploit

## msfconsole

Commandes:

    help
   
Charger un frameworks

    load -l 
    load nessus
    unload nessus 

Utilisation du module FTP anonymous

    search anonymous
    info auxiliary/scanner/ftp/anonymous
    use auxiliary/scanner/ftp/anonymous
    show options
    set RHOSTS 192.168.50.3
    set RPORT 21
    exploit 

Définir une variable globale

    setg RHOSTS 192.168.50.3
    setg RHOST 192.168.50.3

Utiliser un fichier avec les commandes prédéfinies

    resource /root/fichier.txt

Fichier.txt :
    
    use auxiliary/scanner/ftp/anonymous
    set RHOSTS 192.168.50.3
    exploit 

Sinon on peut faire directement msfconsole -r /root/fichier.txt (en dehors de msfconsole)

Charger la base de données metasploit

    /etc/init.d/postegresql start
    msf > db_status
    msfdb init

Ajouter un espace de travail
    
    workspace
    workspace -a metasploitable_vm
    workspace metasploitable_vm
    db_nmap -sV --top-ports 1000 192.168.50.3
    services
    hosts
