Until the containers are up on hub.docker.io you can build the containers here. Go into directory under
containers/ and issue `rake build`.

### Apache Container

If you need SSL look at the `containers/apache/install/002-tbk-ssl.conf` file
and create the necessary keys and certs (TBK-cacert.pem, server.crt and
server.key) and place them in the `install` directory. Otherwise comment out
the SSL references in `containers/apache/Dockerfile` and issue `rake build`.

### Modify docker-compose.yml
Make sure the paths are set correctly in docker-compose.yml

#### Special Note for Linux Users
If you are running Linux any references to `host.docker.internal` need to be
replaced by the IP for the gateway for the network and you need to set
forwarding for the interface. You can do this by setting the DOCKER_INTERNAL
environment variable and setting all interfaces to forwarding.

```
sudo sysctl -w net.ipv4.conf.all.forwarding=1
export DOCKER_INTERNAL=$(docker network inspect docker_tbknet | grep Gate | awk '{print $2}' | sed -e 's/"//g')
```

#### OSX postgres startup issue.
Homebrew will install a launchctl plist file at ~/Library/LaunchAgents that will launch an OS X 
postgres server that will bind to the default postgres port when you login. This will interfere
with the docker based postgresql server. You can stop the OSX based postgresql from starting up via:

```
brew tap homebrew/services
brew services stop postgressql
```
source: https://robots.thoughtbot.com/starting-and-stopping-background-services-with-homebrew


### Container specific ENV vars
The containers use environment variables to set up certain properties of the
containers when they are initialized. If you are using the `dotfiles` repo you
can add the following to your `~/.dotfiles/custom-variables` otherwise add it
to wherever your shell initialization happens.

```
# Password for 'postgres' user when starting Docker
export POSTGRES_PASSWORD=admin
export PGUSER=<your pg user>
export PGPASS=<pg pass to use>
export PGHOST=0.0.0.0
export PGPORT=5432
```

### Starting the Environments

```
# Start PostgreSQL, Apache, and Redis
rake run:core

# Setup PostgreSQL
rake db:setup
```

### Starting the Application Host

If you are running Rails in a Docker container the below applies to you.
Otherwise you can ignore this. I would also not recommend this method any
longer since the Apache proxy is now configured to work with a Rails app server
running natively.

I like to start the application host with a terminal so I can start a tmux
session and have the ability to better watch the output.

* Bring up a new instance of the app container

```
docker-compose run --service-ports --name app app-rails5 bash
```

* A previously created version that has been stopped

```
docker start -ai app
```
