# netbox
# Installation et configuratioin de NetBox 
### Netbox est un outils open-source IPAM (IP Address Management) et DCIM (DataCenter Infrastructure Management).
Il sert à référencer les adresses IP, vlans, devices, racks...

    Mise à jour du système :

 apt update && sudo apt upgrade -y

    Installation des prérequis :

 apt install -y python3 python3-pip \
                   postgresql postgresql-contrib pgadmin4 \
                   nginx certbot python3-certbot-nginx \
                   git curl wget

    Création d'un utilisateur et d'un groupe spécifiques pour NetBox :

 useradd --system --create-home netbox
 groupadd --system netbox

 
  # Configuration de PostgreSQL :
  # Connectez-vous en tant que postgres :

     su - postgres

   #  Créez la base de données NetBox :

    createdb netbox

  # Créez l'utilisateur NetBox avec les permissions nécessaires :

    createuser netbox --createdb --superuser

  # Clonage du dépôt Git de NetBox :

    cd /opt
    sudo git clone https://github.com/netbox-community/netbox.git --branch v3.0.0

# Configuration de Python et installation des dépendances :

    pip3 install --upgrade pip setuptools
    pip3 install -r /opt/netbox/requirements.txt

# Copie du modèle de fichier de configuration :

    cp /opt/netbox/netbox/settings_local.example.py /opt/netbox/netbox/settings_local.py

# Modification du fichier de configuration (/opt/netbox/netbox/settings_local.py) :

    Recherchez les variables suivantes dans le fichier et remplacez leur valeur par ce qui suit :
    ALLOWED_HOSTS = ['your_domain_or_IP']
    DATABASES = {
    'default': {
    'ENGINE': 'django.db.backends.postgresql',
    'NAME': 'netbox',
    'USER': 'netbox',
    'PASSWORD': '<database_password>',
    'HOST': 'localhost',
    'PORT': '',
    }
    }
    SECRET_KEY = '<secret_key>'
    NETBOX_DOMAIN = 'your_domain_or_IP'

# Application des migrations Django :

    ```
    sudo -u netbox python3 manage.py migrate
    ```
# Collecte statique :

    ```
    sudo -u netbox python3 manage.py collectstatic --noinput
    ```

# Configuration de NGINX :

    * Téléchargez le modèle de configuration :

      ```bash
      sudo wget https://raw.githubusercontent.com/netbox-community/netbox-docker/master/examples/production/nginx.conf -P /etc/nginx/
      ```

    * Éditez le fichier de configuration NGINX (`/etc/nginx/nginx.conf`) :

      Remplacez `<your_domain_or_IP>` par votre domaine ou IP dans les deux emplacements.

    * Redémarrage de NGINX :

      ```bash
      sudo systemctl restart nginx
      ```

# Génération du certificat SSL Let's Encrypt via CertBot :

    * Exécutez la commande ci-dessous et fournissez les informations demandées lorsque vous y êtes invité.

      ```bash
      sudo certbot certonly --webroot --email your@email.tld --agree-tos -w /var/www/html -d <your_domain_or_IP>
      ```
Relancement de NetBox :

    ```bash
    sudo systemctl start netbox
    sudo systemctl enable netbox
    ```

# Votre instance NetBox devrait maintenant être accessible depuis votre navigateur Web à l'adresse
`https://<your_domain_or_IP>`. Vous pouvez accéder au panneau d'administration de NetBox en ajoutant `/ui/login/` à l'URL.
