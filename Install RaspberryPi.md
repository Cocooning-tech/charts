# Installation d'un Raspberry Pi 3 et 4
## Installation de l'OS
### Installation d'ubuntu server
#### Gravure et insertion de la SD
Graver une version de l'image disque sur SD card (Balena)
* version __ubuntu-20.04-server__
* version __ubuntu-18.04-server__

Ejecter et rebrancher le lecteur/graveur USB  
Modifier le fichier
* __cmdline.txt__ sur __ubuntu-20.04-server__
* __nobtcmd.txt__ sur __ubuntu-18.04-server__

Ajouter en fin de ligne
<pre><code>cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1
</code></pre>

>Voir si d'autres config sont possibles à ce niveau (WIFI...)

Ejecter la clé  
Insérer la clef dans le nano  
Se connecter en filaire (RJ45)  
Brancher l'alimentation
#### Première connection en SSH
Utiliser Putty (déteminer IP sur port 22)  
login : __ubuntu__  
password : __ubuntu__  
Change le mot de passe et se reconnecter  
Mettre à jour le système
<pre><code>sudo su
apt-get update
apt-get upgrade
</code></pre>

> Sous la version 20.04 il peut être nécessaire de rebooter entre update et upgrade

#### Activer le Wifi
Configurer Netplan  
<pre><code>sudo bash -c "echo 'network: {config: disabled}' > /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg"
sudo nano /etc/netplan/10-my-config.yaml
</code></pre>
Ajouter le code  

>Pas de tabulation dans ce fichier mais des "espaces"

<pre><code>network:
  version: 2
  ethernets:
    eth0:
      dhcp4: true
      optional: true
  wifis:
    wlan0:
      dhcp4: true
      optional: true
      access-points:
        "Livebox-2466":
          password: "S4TVJCQwaWZzknGibt"
</code></pre>
Appliquer la configuration  
<pre><code>sudo netplan generate
sudo netplan apply
</code></pre>
#### Changer le hostname
<pre><code>nano /etc/hostname
</code></pre>
Changer le hostname en fonction du type de noeud
<pre><code>cl-1-master-1
</code></pre>
<pre><code>cl-1-worker-1
</code></pre>

>Les hostname de chaque noeud du cluster doivent être différents

<pre><code>reboot
</code></pre>

#### Mettre à jour la date
A faire
## Installation de k3s
### Mode single node master sans etcd
#### Installation du master node
<pre><code>sudo su
curl -sfL https://get.k3s.io | sh -s - --token tokendetestoauat7579
</code></pre>
#### Installation d'un worker node
<pre><code>sudo su
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.1.71:6443 K3S_TOKEN=tokendetestoauat7579 sh -
</code></pre>
### Mode High Availability with Embedded DB (Experimental) avec etcd
