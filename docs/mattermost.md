

# Mattermost (MM) Local Development Setup

## Set Up MM server

#### 1. Follow steps 1 - 6 at [MM's Dev Server Setup Guide](https://developers.mattermost.com/contribute/server/developer-setup/)

- ❗ File .bashrc is located at `/home/$(whoami)` directory. Run `ls -a` to verify

#### 2. Run `mkdir -p $(go env GOPATH)/src/github.com/{dsv}`, where {dsv} can be anything, but keep consistent throughout

#### 3. Fork and/or clone a specific mattermost server repo you want to test into 

```bash
    $(go env GOPATH)/src/github.com/{dsv}
```

#### 3. Continue from step 9 till end. 

- ❗ Install golang

#### 4. Run `make run-server`, if yields error, proceed through next to step, otherwise skip to [step 6](#6-link-dist-amp-client-as-specified-in-step-5-at-mattermosts-dev-webapp-setup-guide)

#### 5. Install go dep:
```bash
    mkdir $GOPATH/bin
    curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh
```

- ❗(inconsistent results, may yield error, proceed with caution) `cd` into mattermost-server directory and run `dep ensure` to install dependencies. This process might take a while.

#### 6. Link `dist` & `client` as specified in step 5 at [MM's Dev Webapp Setup Guide](https://developers.mattermost.com/contribute/webapp/developer-setup/)

#### 7. Run `make run-server` to run only server, or `make run` to run both server and webapp

<br/>


## Set Up MM Webapp

Follow all steps at [MM's Dev Webapp Setup Guide](https://developers.mattermost.com/contribute/webapp/developer-setup/)

-  Make sure to use the specific repo you want to test instead of mattermost's
- ❗ At step 5, adjust paths to match `$(go env GOPATH)/src/github.com/{dsv}` as specified above
- ❗ DSV's current (as of 2019-12-20) webapp require node v.10.x, either use `nvm` to adjust or update node-sass to support newer versions of node and/or ubuntu. See [issues](#issues) for details
- Run `make test` for testing, `npm run run` or `make run` to run webapp server

For more `make` commands, see [MM's Dev Server Workflow Guide](https://developers.mattermost.com/contribute/server/developer-workflow/)

<br/>

## Issues

Repo|Description|Reason(s)|Solution(s)|Resources
---|---|---|---|---
MM webapp|HTTP 404 on `npm install node-sass`|node-sass v.4.11.x [does not support](https://github.com/sass/node-sass/releases) node >12.0 on linux system|- revert node back to support versions, or<br/> - update node-sass to support newer node?|[1](https://github.com/sass/node-sass/blob/master/TROUBLESHOOTING.md#404-downloading-bindingnode-file)
MM webapp|401 unauthorized response/ JS infinite loop -> browser not responding|user’s credentials are cached|- perform hard reload, if not resolved:<br/>- clear cache manually through browser settings
MM server|`make run-server` fails and outputs `Error starting userland proxy: listen tcp0.0.0.0:3306: bind: address already in use.`|Mysql server running on same port|- uninstall mysql, or<br/>- reconfigure mysql server(?), or<br/>- stop mysql service(?)|[1](https://stackoverflow.com/questions/37896369/error-starting-userland-proxy-listen-tcp0-0-0-03306-bind-address-already-in), [2](https://docs.mattermost.com/developer/dev-setup-troubleshooting.html)
MM server|`make: go: command not found` (on `make run`)|go’s path conflict between `/usr/local/go` and `usr/local/bin/go`|?|[1](https://askubuntu.com/questions/1092589/command-go-not-found), [2](https://tecadmin.net/install-go-on-ubuntu/)
MM server|`/gitlab connect` yields `The redirect URI included is not valid`|?|change “System Console\Web Server\Site URL” to exclude end backslash (\\)|[1](https://gitlab.com/gitlab-org/gitlab-mattermost/issues/84)
MM server|`gitlab[/github] subscribe …` yields `Unable to retreive informations`|- gitlab/github OAuth app of private repo does not give access for MM, or<br/>- repo name is not correct|Modify access given in OAuth app settings

<br/>
<br/>

# Plugin/Webhook Configuration

## Enable Plugin Uploads

It is best to run the server once ([see above](#7-run-make-run-server-to-run-only-server-or-make-run-to-run-both-server-and-webapp)) for initialization before editing `config.json`. When done, proceed to the following steps:

- navigate to `{mattermost-server-path}/config/` and make the following change:

    ```json
        {
            "PluginSettings": {
                "EnableUploads": true
            }
        }
    ```

    - ❗ If you follow the steps above for local development, `{mattermost-server-path}` should be `$(go env GOPATH)/src/github.com/{dsv}`. In production, it should be `/opt/mattermost` if you follow [MM's ubuntu installation guide](https://docs.mattermost.com/install/install-ubuntu-1804.html).

- restart server

<br/>

## Mattermost Plugin/Webhook List

Name        |Type       |Author         |Language   |Repo/Source
------------|-----------|---------------|-----------|------------
Reminder    |Plugin     | Mattermost    |Golang     |https://github.com/scottleedavis/mattermost-plugin-remind      
Github      |Plugin     | Mattermost    |Golang     |https://github.com/mattermost/mattermost-plugin-github     
Gitlab      |Plugin     | Mattermost    |Golang     |https://github.com/mattermost/mattermost-plugin-gitlab     
Bitbucket   |Webhook    | cvitter       |Python     |https://github.com/cvitter/mattermost-bitbucket-bridge 


## Set up Bitbucket's Webhook ([cvitter's implementation](https://github.com/cvitter/mattermost-bitbucket-bridge))



### 1. Install Python && Dependencies
- install python3 (if not already), following [this instruction](https://vitux.com/install-python3-on-ubuntu-and-set-up-a-virtual-programming-environment/)

❗ Cvitter's implementation does not currently support all [bitbucket events](https://github.com/cvitter/mattermost-bitbucket-bridge#supported-bitbucket-events), some will yield log error & print trace stack but should not interrupt python webhook nor mattermost server.


- run
    ```bash
        sudo apt install python3-pip #(if not already installed)
        sudo pip3 install flask 
        sudo pip3 install requests
        #or any other dependencies that future version of this repo might require
    ```

### 2. Run Python Flask Server

- Follow steps [here](https://github.com/cvitter/mattermost-bitbucket-bridge#setup-the-flask-application)

  - ❗ You can simply start app with `sudo python bitbucket.py` for easier debugging.

  - If your mattermost server lives at port `8065` as default, you might set your python app at port `8064`. Remember this port for next step.

  - ❗If developing locally, use [ngrok](https://dashboard.ngrok.com/) to expose server path, otherwise, see the following steps to set up nginx proxy

### 3. (if necessary) Set Up Nginx Proxy

- open editor (`vi`, `nano`, remote vscode, ...) at:

    ```bash
        /etc/nginx/sites-available/{appropriate-config-file}
    ```
    where `{appropriate-config-file}` varies depending on server settings & routing and might be one of the following:

    - `default`, if you want python server to live on something like `https://domain.com/python-server`

    - `subdomain.domain.com` (*depends on your naming convention*), if you want python server to live on a subdomain

- proxy  a path to server. For example, for python server to live at `https://mattermost.domain.com/bitbucket-hook`, create and/or edit `mattermost.domain.com`:

    ```nginx
    #...
    server {
        root /var/www/mattermost.domain.com;
        index index.html index.htm index.nginx-debian.html;

        server_name mattermost.domain.com;
        #merge_slashes off;

        location / {
            proxy_pass http://localhost:8065 # or a custom port for mattermost server 
            #...
        }

        location /bitbucket-hook {
            rewrite ^bitbucket-hook(.*) /$1 break;
            proxy_pass http://localhost:8064 # or a custom port for python app
            #...
        }   
            
        #...
    }
    #...
    ```
    - ❗ every time done editing nginx config, run
        ```bash
        sudo nginx -t #to test config files
        sudo systemctl restart nginx
        ```

    - the first `location` context is the proxy for your mattermost server, the second is for bitbucket webhook python app. Both live on the same subdomain.
    - `rewrite` rule is to rewrite request url to the correct corresponding server endpoint. If not specified, for example, `https://mattermost.domain.com/bitbucket-hook` will route to `http//localhost:8064/bitbucket-hook` instead of only `http//localhost:8064`. See [here](https://github.com/cvitter/mattermost-bitbucket-bridge#setup-the-flask-application) for more info
    - ❗ if experiencing **multiple slashes** on http request path (verify by `GET` the path and look at logs of python app), for example:
        ```bash
            #GET request to
            https://mattermost.domain.com/bitbucket-hook/hooks/[hookcode]
            #might yield 404 and log to
            https://mattermost.domain.com/bitbucket-hook//hooks/[hookcode]
        ```

        then add or uncomment `merge_slashes off;` within the same `server` context of the two `location` blocks above.

        if issue is still not resolved, edit bitbucket webhook url to the following format:
        ```http
        https://mattermost.domain.com/bitbucket-hook//hooks/[hookcode]
        ```

        if none fixes, look through the following and document for future development
        - https://gist.github.com/soheilhy/8b94347ff8336d971ad0
        - https://gist.github.com/JustThomas/74d04d0ae542b6dab9495bce24c0741c
        - https://stackoverflow.com/questions/4320774/nginx-how-to-keep-double-slashes-in-urls
        - http://nginx.org/en/docs/http/ngx_http_core_module.html#merge_slashes

<br/>
<br/>

# Useful Linux (Ubuntu 18.04 tested) commands

## Show which port what is running on
```bash

#see what ports are open
nmap -sS -O 127.0.0.1
#or
netstat -nap
#or
netstat -tlnp

#alternatively, for mysql:
sudo mysql
mysql> SHOW GLOBAL VARIABLES LIKE 'PORT';
```

Resources: [1](https://serverfault.com/questions/116100/how-to-check-what-port-mysql-is-running-on), [2](https://www.linux-noob.com/forums/topic/1262-check-what-ports-are-open/)

## Start/stop/restart mysql service
```bash
#start/stop from the command line: 

/etc/init.d/mysqld start 
/etc/init.d/mysqld stop 
/etc/init.d/mysqld restart 

#Some Linux flavours offer the service command too service 

mysqld start service 
mysqld stop service 
mysqld restart 

#or 

service mysql start 
service mysql stop 
service mysql restart

```

[Source](https://tableplus.com/blog/2018/10/how-to-start-stop-restart-mysql-server.html)

## Stop/remove all docker containers
```bash
docker stop $(docker ps -a -q) 
docker rm $(docker ps -a -q)
```

Resources: [1](https://linuxize.com/post/how-to-remove-docker-images-containers-volumes-and-networks/), [2](https://docs.docker.com/engine/reference/commandline/kill/)

