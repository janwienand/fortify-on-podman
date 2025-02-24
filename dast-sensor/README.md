# ScanCentral DAST Scan Machine
## ❓ Description

This repo contains the image required for a Podman ScanCentral DAST Scan Machine Database deployment.
The tables are created at first startup.

## 🎉 Deployment

### 🐳 Offline Image loading

If you plan to deploy this component on an offline environment, below are the commands to save the Docker image for
loading in the destination environment:

1. Save the image locally
    ```
    podman save fortifydocker/dast-scanner:<tag> -o dast-scanner.tar
    podman save fortifydocker/wise:<tag> -o wise.tar
    podman save fortifydocker/fortify-2fa:<tag> -o fortify-2fa.tar
    podman save mcr.microsoft.com/mssql/server:<tag> -o mssql.tar
    ```
2. Get this tar file in the destination environment, and load it
    ```
    podman load -i dast-scanner.tar
    podman load -i wise.tar
    podman load -i fortify-2fa.tar
    podman load -i mssql.tar
    ```

    **_NOTE:_** This step is not required if you have a direct connection to Docker Hub.


### 🐳 Execution using docker-compose

1. Create a `scandata` folder and set write permissions to your container
    ```shell
    sudo chmod -R 777 scandata
    ```
2. Run the following command
   ```sh
   podman compose up -d
   ```
   
   You can tail the logs using the following command:
   ```sh
   podman compose logs -f
   ```
3. When the ScanCentral DAST Scan Machine is up you can see it in the ScanCentral DAST webinterface