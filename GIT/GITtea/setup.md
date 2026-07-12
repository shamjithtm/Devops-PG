Setup GIT tea
#########################################################################################################################################s

```
mkdir gitea-docker && cd gitea-docker
```



Create a docker-compose.yml file:
```

YAML
version: '3.8'

services:
  server:
    image: gitea/gitea:latest
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - GITEA__database__DB_TYPE=postgres
      - GITEA__database__HOST=db:5432
      - GITEA__database__NAME=gitea
      - GITEA__database__USER=gitea
      - GITEA__database__PASSWD=secret_db_password
    restart: always
    volumes:
      - ./gitea-data:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "3000:3000"
      - "2222:22"
    depends_on:
      - db

  db:
    image: postgres:15-alpine
    container_name: gitea_db
    restart: always
    environment:
      - POSTGRES_USER=gitea
      - POSTGRES_PASSWORD=secret_db_password
      - POSTGRES_DB=gitea
    volumes:
      - ./postgres-data:/var/lib/postgresql/data



```












Start the containers:


```
docker compose up -d
```


<img width="1893" height="903" alt="image" src="https://github.com/user-attachments/assets/fe791397-b721-4ed6-b8ac-124cdfe9e324" />




Step 2: Enable Gitea Actions & Set Up the Runner
Gitea Actions (which uses GitHub Actions syntax) is disabled by default.

1. Enable Actions in Gitea
Open the Gitea configuration file located at ./gitea-data/gitea/conf/app.ini on your host machine and add the following lines at the bottom:

Ini, TOML
[actions]
ENABLED = true
Restart your Gitea container to apply changes:


docker compose restart server




<img width="1919" height="754" alt="image" src="https://github.com/user-attachments/assets/90ec2684-c4a2-47ce-8313-73e08780999c" />




2. Get the Runner Registration Token
Log into Gitea as an admin.

Go to Site Administration -> Actions -> Runners.

Click Create New Runner and copy the Registration Token.



docker run -d \
  --name gitea_runner \
  -e GITEA_INSTANCE_URL=http://<your-gitea-ip-or-container-name>:3000 \
  -e GITEA_RUNNER_REGISTRATION_TOKEN=<YOUR_COPIED_TOKEN> \
  -e GITEA_RUNNER_NAME=my-docker-runner \
  -e GITEA_RUNNER_LABELS=ubuntu-latest:docker://node:18-bullseye,ubuntu-22.04:docker://node:18-bullseye \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --restart always \
  gitea/act_runner:latest


docker run -d \
  --name gitea_runner \
  -e GITEA_INSTANCE_URL=http://gitea:3010 \
  -e GITEA_RUNNER_REGISTRATION_TOKEN=gAGqyyi3FPeo5ssNPqzAlmKtfndeCx3mIUXSqYlT \
  -e GITEA_RUNNER_NAME=gittea-runner-hs2 \
  -e GITEA_RUNNER_LABELS=ubuntu-latest:docker://node:18-bullseye,ubuntu-22.04:docker://node:18-bullseye \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --restart always \
  gitea/act_runner:latest



1. Close Public Registration (Crucial!)
By default, anyone who finds your Gitea URL can click "Register" and create an account on your server. While they still won't see your private repos, they can use your server's resources to host their own stuff.

You should disable public registration immediately. Open your ./gitea-data/gitea/conf/app.ini file and change or add this setting under the [service] block:

Ini, TOML
```
[service]
DISABLE_REGISTRATION = true
ALLOW_ONLY_EXTERNAL_REGISTRATION = false
```


Clone the Repo in Local 

```
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Copy the public and add in the profile 

Step 4: Add the key to Gitea
Log in to Gitea.
Click your avatar → Settings.
Go to SSH / GPG Keys.
Click Add Key.
Paste the public key and save.


Alllow 2222 in security group then 

git clone ssh://git@50.18.206.90:2222/patientgain/test.git

<img width="872" height="239" alt="image" src="https://github.com/user-attachments/assets/853ce586-8103-4d4d-9303-992a11a931ce" />


<img width="914" height="93" alt="image" src="https://github.com/user-attachments/assets/a948f2af-73d3-4dac-a715-d1391413016d" />



```
git branch -a
git remote -v
On branch main

```
then 
```

git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin ssh://git@50.18.206.90:2222/patientgain/test.git
git push -u origin main

```


CI CD Pipe line 


Step 1: Verify the runner is connected


Go to Site Administration (or Repository Settings if it's a repo-level runner).
Open Actions → Runners.


here it not showing so  we need troubleshoot 

docker logs gitea_runner


Cannot ping the Gitea instance server
lookup gitea on 172.31.0.2:53: no such host


docker inspect gitea_runner | grep GITEA
                "GITEA_RUNNER_NAME=gittea-runner-hs2",
                "GITEA_RUNNER_LABELS=ubuntu-latest:docker://node:18-bullseye,ubuntu-22.04:docker://node:18-bullseye",
                "GITEA_INSTANCE_URL=http://gitea:3010",
                "GITEA_RUNNER_REGISTRATION_TOKEN=gAGqyyi3FPeo5ssNPqzAlmKtfndeCx3mIUXSqYlT",




First, check your containers

Run:

docker ps

I want to see:

The container name for Gitea.
Which ports are published.



root@ip-172-31-4-207:/var/www/docker/gitea-docker# docker inspect gitea --format='{{json .NetworkSettings.Networks}}'
{"gitea-docker_default":{"IPAMConfig":null,"Links":null,"Aliases":["gitea","server"],"MacAddress":"42:92:6b:f9:40:e0","DriverOpts":null,"GwPriority":0,"NetworkID":"1e7d3132967782415cad2236a7c9073ab8ba02d754515d9f1d264c1707d13431","EndpointID":"1abb21cb457dac77638ffcee84b2db2ebedd856564a0962dcfcfc72c38547482","Gateway":"172.28.0.1","IPAddress":"172.28.0.3","IPPrefixLen":16,"IPv6Gateway":"","GlobalIPv6Address":"","GlobalIPv6PrefixLen":0,"DNSNames":["gitea","server","f9cf3be4dbbf"]}}


it shoukd be same network 

docker network connect gitea-docker_default gitea_runner

docker restart gitea_runner





 ✘ Contain... Error response from daemon: Conflict. The container name "/gitea_runner" is already in use by container "5fbe9a6dadc08934f72e647ba2abc8f664dc026bb59dfa0b4a053fdd59cd42bd". You have to remove (or rename) that container to be able to reuse that name. 0.0s
Error response from daemon: Conflict. The container name "/gitea_runner" is already in use by container "5fbe9a6dadc08934f72e647ba2abc8f664dc026bb59dfa0b4a053fdd59cd42bd". You have to remove (or rename) that container to be able to reuse that name.
root@ip-172-31-4-207:/var/www/docker/gitea-docker# git rm 5fbe9a6dadc08934f72e647ba2abc8f664dc026bb59dfa0b4a053fdd59cd42bd


docker stop gitea_runner
docker rm gitea_runner


Fixed Docker-compose with runner 



services:
  server:
    image: gitea/gitea:latest
    container_name: gitea
    restart: always
    environment:
      USER_UID: "1000"
      USER_GID: "1000"
      GITEA__database__DB_TYPE: postgres
      GITEA__database__HOST: db:5432
      GITEA__database__NAME: gitea
      GITEA__database__USER: gitea
      GITEA__database__PASSWD: mVGVM7qeZH11
    volumes:
      - ./gitea-data:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "3010:3000"
      - "2222:22"
    depends_on:
      - db

  db:
    image: postgres:15-alpine
    container_name: gitea_db
    restart: always
    environment:
      POSTGRES_USER: gitea
      POSTGRES_PASSWORD: mVGVM7qeZH11
      POSTGRES_DB: gitea
    volumes:
      - ./postgres-data:/var/lib/postgresql/data

  gitea_runner:
    image: gitea/act_runner:latest
    container_name: gitea_runner
    restart: always
    depends_on:
      - server
    environment:
      GITEA_INSTANCE_URL: http://server:3000
      GITEA_RUNNER_REGISTRATION_TOKEN: ${GITEA_RUNNER_TOKEN}
      GITEA_RUNNER_NAME: gittea-runner-hs2
      GITEA_RUNNER_LABELS: ubuntu-latest:docker://node:18-bullseye,ubuntu-22.04:docker://node:18-bullseye
    volumes:
      - ./runner:/data
      - /var/run/docker.sock:/var/run/docker.sock

















      docker logs -f gitea_runner

      Waiting to retry ...
level=info msg="Registering runner, arch=amd64, os=linux, version=v0.6.1."
level=error msg="Invalid input, please re-run act command." error="token is empty"
Error: token is empty
Waiting to retry ...




nano .env

GITEA_RUNNER_TOKEN=TQTank2UhiOldL0BdB40puh7d8doI2HsVUzaki4Y

docker logs -f gitea_runner

SUCCESS
time="2026-07-12T08:28:20Z" level=info msg="Starting runner daemon"
time="2026-07-12T08:28:20Z" level=info msg="runner: gittea-runner-hs2, with version: v0.6.1, with labels: [ubuntu-latest ubuntu-22.04], declare successfully"





 
then  Run to check all variable in env taken or not

docker compose config
