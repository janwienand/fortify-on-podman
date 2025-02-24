# ScanCentral DAST PostgreSQL Database
## ❓ Description

This repo contains the image required for a Podman ScanCentral DAST PostgreSQL Database deployment.
The tables are created at first startup.

## 🎉 Deployment

### 🐳 Offline Image loading

If you plan to deploy this component on an offline environment, below are the commands to save the Docker image for
loading in the destination environment:

1. Save the image locally
    ```
    podman save docker.io/bitnami/postgresql:15 -o dast-postgres.tar
    ```
2. Get this tar file in the destination environment, and load it
    ```
    podman load -i dast-postgres.tar
    ```

    **_NOTE:_** This step is not required if you have a direct connection to Docker Hub.


### 🐳 Execution using docker-compose

1. Review and edit [docker-compose.yml](docker-compose.yml)
2. Copy or rename [.env.template](.env.template) to `.env`. Review and edit the `.env` file if needed
    
    **_NOTE:_** Please make sure to delete all in line comments
3. Create a `data` folder and set write permissions to your container
    ```shell
    sudo chmod -R 777 data
    ```
4. Run the following command
   ```sh
   podman compose up -d
   ```
   
   You can tail the logs using the following command:
   ```sh
   podman compose logs -f
   ```
5. When the db app is up, you can use it to run ScanCentral DAST