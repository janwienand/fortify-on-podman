# Fortify Software Security Center (SSC) MySQL Database
## ❓ Description

This repo contains the files required for a Podman Fortify Software Security Center MySQL Database deployment.

## 🎉 Deployment

### 🐳 Offline Image loading

If you plan to deploy this component on an offline environment, below are the commands to save the Docker image for
loading in the destination environment
1. Save the image locally
```
podman save mysql:8.0.31 -o ssc-mysql.tar
```
2. Get this tar file in the destination environment, and load it
```
podman load -i ssc-mysql.tar
```

**_NOTE:_**  This step is not required if you have direct connection to Docker Hub.


### 🐳 Execution using podman compose

1. Review and edit `docker-compose.yml`, `secrets/config-file.cnf`
2. Copy or rename [.env.template](.env.template) to `.env`. Review and edit the `.env` file if needed
3. Set write permissions to your container:
```sh
sudo chmod -R 777 data secrets
```
4. Run the following command
```
podman compose up -d

# You can tail the logs using the following command
docker-compose logs -f
```
5. The database will be ready when the following lines are logged
```
[System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
```
6. When the db app is up, you can use it to run Fortify Software Security Center

### 🌡️ Environment variables

| Name                | Description                    | Example    |
|---------------------|--------------------------------|------------|
| MYSQL_DATABASE      | Name for the MySQL database    | ssc        |
| MYSQL_ROOT_PASSWORD | Root password for the database | mypassword |
| MYSQL_USER          | MySQL Username                 | fortify    |
| MYSQL_PASSWORD      | MySQL Passord                  | mypassword |