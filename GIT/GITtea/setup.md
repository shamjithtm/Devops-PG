Setup GIT tea
#########################################################################################################################################s

mkdir gitea-docker && cd gitea-docker



Create a docker-compose.yml file:

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















Start the containers:


docker compose up -d


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
[service]
DISABLE_REGISTRATION = true
ALLOW_ONLY_EXTERNAL_REGISTRATION = false


Clone the Repo in Local 

ssh-keygen -t ed25519 -C "your_email@example.com"

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






