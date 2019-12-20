

# MM Local Development Setup

## Set Up MM server

#### 1. Follow steps 1 - 6 at [Mattermost's Dev Server Setup Guide](https://developers.mattermost.com/contribute/server/developer-setup/)

- ❗ File .bashrc is located at home/{user} directory. Run `ls -a` to verify

#### 2. Run `mkdir -p $(go env GOPATH)/src/github.com/{dsv}`, where {dsv} can be anything, but keep consistent throughout

#### 3. Fork and/or clone a specific mattermost server repo you want to test into `$(go env GOPATH)/src/github.com/{dsv}` 

#### 3. Continue from step 9 till end. 

- ❗ Install golang

#### 4. Run `make run-server`, if yields error, proceed through next to step, otherwise skip to (6)


#### 5. Install go dep:
```bash
    mkdir $GOPATH/bin
    curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh
```

- ❗(inconsistent results, may yield error, proceed with caution) `cd` into mattermost-server directory and run `dep ensure` to install dependencies. This process might take a while.

#### 6. Link `dist` & `client` as specified in step 5 at [Mattermost's Dev Webapp Setup Guide](https://developers.mattermost.com/contribute/webapp/developer-setup/)

#### 7. Run `make run-server` to run only server, or `make run` to run both server and webapp


## Set Up MM Webapp

Follow all steps at [Mattermost's Dev Webapp Setup Guide](https://developers.mattermost.com/contribute/webapp/developer-setup/)

-  Make sure to use the specific repo you want to test instead of mattermost's
- ❗ At step 5, adjust paths to math `$(go env GOPATH)/src/github.com/{dsv}` as specified above
- ❗ DSV's current (as of 2019-12-20) webapp require node v.10.x, either use `nvm` to adjust or update node-sass to support newer versions of node and/or ubuntu. See issue {#TODO: add link to issue} for details
- Run `make test` for testing, `npm run run` or `make run` to run webapp server

For more `make` commands, see [Mattermost's Dev Server Workflow Guide](https://developers.mattermost.com/contribute/server/developer-workflow/)