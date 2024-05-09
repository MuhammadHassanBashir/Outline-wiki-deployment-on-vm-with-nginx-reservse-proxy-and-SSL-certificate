# Outline-wiki-deployment-on-vm-with-nginx-reservse-proxy-and-SSL-certificate

## 1- GCP VM and domain mapping

-    Create a simple VM in gcp cloud 
-    Attach static to the vm
-    Allow port 80 , 443 , 22 , 3000 port from gcp firewall and attach tag under gcp vm network tag.
-    Then go to cloud DNS under hosted zone and point domain to vm static ip. In my case my domain is docs.disearch.ai
-    then ssh the vm and upload docker-compose.yaml and docker.env file to vm

## 2- Now go to the slack page and do some configuration. and add information in you docker.env. so that your outline will successfully redirect to slack. steps are given below..

### Creat Environment File for Docker by adding Slack authentication configuration.

-   Sign in with your Slack account and visit ***https://api.slack.com/apps***
-   then click on **create an app**
-   then click on **from scratch**
-   then **give app name and pick a workspace to develop your app in**
-   once done then fill a **display information(App name , short discription , and background**)
-   then click on save
-   **then Select OAuth and Permissions option from the left sidebar.** and under the **Redirect URLs** give the URL on which your outline application is listining. In my case it is **https://docs.disearch.ai/auth/slack.callback** or **http://docs.disearch.ai/auth/slack.callback**. then Click on the Save URLs button to proceed. 
-   then Scroll down to the **User Token Scopes section** of the page and select the following scopes from the dropdown menu.
    -  identity.avatar
    -  identity.basic
    -  identity.email
    -  identity.team
- then go to the **Basic Information** page from the left sidebar. Copy the values **Client ID and Client Secret, App ID and Verification Token values** from their boxes under **App Credentials**. and add this information to your **docker.env**
- now click on **slash command** option from the left sidebar. and click on **create new command**.
    - Command: /outline
    - Request URL: https://docs.disearch.ai/api/hooks.slack
    - then enter description for the command and word hint and click on save to finish.
-  then open  the menu Features >> Interactivity and Shortcuts from the left sidebar. **Enable Interactivity by switching the toggle button** and paste **https://docs.disearch.ai/api/hooks.interactive** as the Request URL. Click the Save Changes button to finish.
-  then open the **Settings >> Install App** page from the left sidebar and click on the **Install** to WorkSpace button to install the App for your Slack workspace.


## 3- Now run below command to start docker compose.

### Create the database with the following command:

    sudo docker-compose run outline yarn db:create --env=production-ssl-disabled

### Migrate the new database to add needed tables, indexes, etc:

    sudo docker-compose run outline yarn db:migrate --env=production-ssl-disabled

### Running

Make sure you are in the same directory as docker-compose.yml and start Outline:

    sudo docker-compose up -d

    sudo docker ps   # till here your outline container , postgress , redis should be running. 

## 4- Nginx and Reverse proxy installation on vm

### Nginx installation

    sudo apt update
    sudo apt install nginx
    sudo systemctl start nginx  


### Reverse proxy 

    sudo nano /etc/nginx/sites-available/docs_disearch.conf
    
    server {
    listen 80;
    server_name docs.disearch.ai;

    location / {
        proxy_pass http://docs.disearch.ai:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    }

    sudo ln -s /etc/nginx/sites-available/docs_disearch.conf /etc/nginx/sites-enabled/

    sudo nginx -t

    sudo systemctl restart nginx

            
