# Installation d'un Environnement VoIP avec Asterisk
Ce projet a pour but de créer un environnement VoIP pour un réseau local, de déployer cette infrastructure dans un environnement cloud et de connecter deux infrastructures VoIP situées sur des sites distants à l'aide d'un VPN.

Table des Matières
Installation de VirtualBox
Installation d'Ubuntu
Configuration Réseau
Installation d'Asterisk
Téléchargement des fichiers de configuration
Connexion des Clients Softphone
Déploiement dans une Infrastructure Cloud
Interconnexion de Deux Sites Distants via VPN
Support
Installation de VirtualBox
Téléchargez et installez VirtualBox à partir du site officiel : VirtualBox

Installation d'Ubuntu
Téléchargez Ubuntu à partir du lien suivant : Télécharger Ubuntu ou via Torrent : Ubuntu Torrent

Pour une aide à l'installation d'Ubuntu, consultez cette vidéo explicative : Installation Ubuntu

Configuration Réseau
Pour attribuer une adresse IP privée à votre machine virtuelle et la connecter directement au modem, allez dans "CONFIGURATION" -> "Réseau" -> Mode d'accès réseau : "Accès par pont".

Installation d'Asterisk
Mettez à jour votre système :

sh
Copier le code
sudo apt update && sudo apt upgrade
Installez les outils nécessaires :

sh
Copier le code
sudo apt install net-tools wget build-essential subversion
Vérifiez l'adresse IP de votre machine :

sh
Copier le code
ifconfig
Téléchargez et installez Asterisk :

sh
Copier le code
cd /usr/src/
sudo wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-18-current.tar.gz
sudo tar zxf asterisk-18-current.tar.gz
cd asterisk-18.*/
sudo contrib/scripts/get_mp3_source.sh
sudo contrib/scripts/install_prereq install
sudo ./configure
sudo make menuselect
sudo make -j2
sudo make install
sudo make samples
sudo make config
sudo ldconfig
sudo systemctl start asterisk
sudo asterisk -vvvr
exit
sudo ufw allow 5060/udp
cd /etc/asterisk
sudo chmod 777 *
Téléchargement des fichiers de configuration
Téléchargez les fichiers sip.conf, extensions.conf et voicemail.conf à partir de ce lien et copiez-les dans le répertoire /etc/asterisk.

Ensuite, exécutez les commandes suivantes :

sh
Copier le code
sudo asterisk -vvvr
module load chan_sip.so
reload
sip show peers
Connexion des Clients Softphone
Pour vous connecter, utilisez un softphone (3cxphone, Zoiper, microSIP sur PC ; mizudroid, Zoiper sur téléphone). Vous aurez besoin de l'ID client et du mot de passe contenu dans sip.conf. Exemple :

client 1: id: 7001, mdp: 7001
client 2: id: 7002, mdp: 7002
Vidéos d'aide
Vidéo 1
Vidéo 2 (à partir de 4min20)
Vidéo 3 (configuration de softphone 3cx)
Déploiement dans une Infrastructure Cloud
Prérequis
Un compte chez un fournisseur de services cloud (AWS, Google Cloud, Azure, etc.).
Une machine virtuelle configurée avec Ubuntu.
Étapes
Création de la Machine Virtuelle:

Connectez-vous à votre fournisseur de services cloud et créez une nouvelle instance de machine virtuelle en utilisant l'image Ubuntu 20.04 LTS.
Assurez-vous que les ports nécessaires (ex. 5060/udp pour SIP) sont ouverts dans le pare-feu de votre instance.
Accès à la Machine Virtuelle:

Connectez-vous à votre machine virtuelle via SSH.
Suivez les mêmes étapes d'installation d'Asterisk comme mentionné dans la section précédente.
Configuration du Pare-feu:

Configurez les règles du pare-feu pour permettre le trafic VoIP. Par exemple, pour AWS, cela peut se faire via les groupes de sécurité.
Exemple de commande pour UFW :
sh
Copier le code
sudo ufw allow 5060/udp
Vérification des Services:

Assurez-vous qu'Asterisk fonctionne correctement et que les clients peuvent se connecter via Internet.
Utilisez les commandes suivantes pour vérifier les services :
sh
Copier le code
sudo systemctl status asterisk
sudo asterisk -vvvr
sip show peers
Interconnexion de Deux Sites Distants via VPN
Prérequis
Deux réseaux locaux avec des serveurs Asterisk.
Un service VPN (OpenVPN, IPsec, WireGuard, etc.) configuré entre les deux réseaux.
Étapes
Configuration du VPN:

Configurez un tunnel VPN entre les deux réseaux locaux. Assurez-vous que les deux serveurs Asterisk peuvent se voir à travers le VPN.
Utilisez OpenVPN, IPsec, ou WireGuard pour établir le VPN. Voici un guide de base pour OpenVPN :
Installez OpenVPN :
sh
Copier le code
sudo apt install openvpn
Configurez le serveur OpenVPN sur un des sites et le client OpenVPN sur l'autre site en suivant la documentation officielle d'OpenVPN.
Configuration d'Asterisk pour l'IAX:

Modifiez le fichier iax.conf sur chaque serveur Asterisk pour permettre la communication entre les deux serveurs via le VPN.
Exemple de configuration dans iax.conf :
ini
Copier le code
[serverB]
type=peer
host=IP_DU_SERVEUR_B
secret=PASSWORD
context=from-internal
Vérification de la Connectivité:

Assurez-vous que les serveurs Asterisk peuvent communiquer entre eux via le protocole IAX :
sh
Copier le code
sudo asterisk -vvvr
iax2 show peers
Les deux serveurs doivent apparaître comme connectés.
Routage des Appels:

Configurez le routage des appels dans les fichiers extensions.conf pour permettre les appels entre les deux sites.
Exemple de configuration dans extensions.conf :
ini
Copier le code
exten => _X.,1,Dial(IAX2/serverB/${EXTEN})
Support
Pour toute question ou problème, vous pouvez me contacter via :

Facebook : Sofiane Chikh
Email : sofianechikhso@gmail.com
