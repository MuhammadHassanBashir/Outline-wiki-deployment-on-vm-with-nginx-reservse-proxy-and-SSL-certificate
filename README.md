# Outline-wiki-deployment-on-vm-with-nginx-reservse-proxy-and-SSL-certificate

- First to all go to the slack page and do some configuration. and add information in you docker.env. so that your outline will successfully redirect to slack. steps are given below..

##Creat Environment File for Docker by adding Slack authentication configuration.

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


## Now run below command to start docker compose.

### Create the database with the following command:

  docker compose run --rm outline yarn db:create --env=production-ssl-disabled

### Migrate the new database to add needed tables, indexes, etc:

  docker compose run --rm outline yarn db:migrate --env=production-ssl-disabled

### Running

Make sure you are in the same directory as docker-compose.yml and start Outline:

  docker compose up -d

                
