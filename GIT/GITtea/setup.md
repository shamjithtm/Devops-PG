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



