# odoo17-to-18-community-upgrade-docker
July 28th 2025

I couldn't find clear explanations and I don't have a devl odoo env.
This is a description of the actions I did to upgrade a odoo community v17 to v18 with standard docker containers

0) to avoid any module problem, remove the custom V17 installations:
    - in the odoo env, go to apps, look for all modules installed and remove the custom ones. You may find the list in your ./custom (the folder which links to /mnt/extra-addons in your docker-compose)

2) upgrade your current env to last V17 version (see in V17 folder):
    - with provided latest V17 Dokerfile, entrypoint.sh and wait-for-psql.py, create new image, run existing V17 on a docker from that new image
        * see in V17 folder the build command and docker-compose.yml odoo.conf related
        * remember on which docker runs your pstgres (in my case postgres-20250725-mylucdatabase)
       
4) Create a Migration env based on V18 called V18-MIG (with provided latest V18 Dokerfile):
    
    a) update your V18-MIG Dockerfile
   
       - add vim, curl, git, unzip in the early installation
       - create a directory for OpenUpgrade scripts (line 95)
       - add the install of openupgradelib (line 96)
       - add the reference to directory /var/lib/odoo/OpenUpgrade (line 98)
   
    b) update your V18-MIG entrypoint.sh
   
       - you need to add the migration instruction (see at the end) 
   
    c) created or update you V18-MIG docker-compose.yml
   
       - you need to run once only (==> no restart)
       - you need to add reference to the local ./OpenUpgrade
   
    d) update your V18-MIG odoo.conf with references to the running postgresql
   
       - you need to reference your V17 postgresql running docker and the db_name to migrate
       - you need to add variables
        
    e) download the OCA OpenUpgrade Scripts in your V18-MIG folder
   
        - download and unzip or git clone the OpenUpgrade V18 env to the local folder (in my case ./OpenUpgrade/ )
   
    e) run the migration:
   
        - Build your image for migration (see command in build.odoo.image.txt)
        - launch the docker (docker-compose up -d) 
        - monitor the logs (tail -f ./logs/odoo.log)
   
   
6) Create a V18 env based on V18 (see in V18 folder):
   
   - Get the latest V18 Dokerfile, entrypoint.sh and wait-for-psql.py
   - copy the odoo-filestore content to the new V18 folder
   - Build your docker image
   - Create your docker-compose.yml with only the odoo service (postgres already running) 
   - run the new V18 docker (docker-compose up -d)
   - monitor the logs (tail -f ./logs/odoo.log)

links : 
   odoo Docker images : https://hub.docker.com/_/odoo
   OCA Openupgrade : https://github.com/OCA/OpenUpgrade/tree/18.0

This is provided as indication only, HTH, best, Luc
