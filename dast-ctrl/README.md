# ScanCentral DAST Controller
## ❓ Description

This repo contains the files required for a Podman Fortify ScanCentral DAST deployment.

## 🎉 Deployment

### 🐳 Offline image retrieval

If you plan to deploy this component on an offline environment, below are the commands to save the Docker image for
loading in the destination environment:

1. First login to Docker Hub using your Docker Hub credentials
    ```
    podman login -u <Docker hub username>
    ```
2. Then save the image locally
    ```
    podman save fortifydocker/scancentral-dast-config:<tag> -o scancentral-dast-config.tar
    podman save mcr.microsoft.com/mssql/server:<tag> -o mssql.tar
    podman save fortifydocker/dast-scanner:<tag> -o dast-scanner.tar
    podman save fortifydocker/scancentral-dast-api:<tag> -o scancentral-dast-api.tar
    podman save fortifydocker/scancentral-dast-globalservice:<tag> -o scancentral-dast-globalservice.tar
    ```
3. Get this tar file in the destination environment, and load it
    ```
    podman load -i scancentral-dast-config.tar
    podman load -i mssql.tar
    podman load -i dast-scanner.tar
    podman load -i scancentral-dast-api.tar
    podman load -i scancentral-dast-globalservice.tar
    ```

    **_NOTE:_**  This step is not required if you have direct connection to Docker Hub

### 🐳 Deployment

1. Copy or rename [.env.template](.env.template) to `.env`. Review and edit the `.env` file

   **_NOTE:_** Please make sure to delete all in line comments
2. Create DAST API Service Certificate
   ```shell
   API_SERVER_CERT_PWD="$(openssl rand -base64 32)"

   openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 \
   -keyout secrets/api-server-key.pem \
   -out secrets/api-server-cert.pem \
   -subj "/C=CA/ST=Ontario/L=Waterloo/O=OpenText/OU=IT"
   
   openssl pkcs12 -export \
   -out secrets/api-server-cert.pfx \
   -inkey secrets/api-server-key.pem \
   -in secrets/api-server-cert.pem  \
   -passout "pass:${API_SERVER_CERT_PWD}"
   
   echo "Paste the following value in .env > DAST_API_SSL_CERT_PASSWORD : $API_SERVER_CERT_PWD" 
   ```

3. Create DAST Utility Service Certificate
   ```shell
   UTILITYSERVICE_SERVER_CERT_PWD="$(openssl rand -base64 32)"

   openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 \
   -keyout secrets/utilityservice-server-key.pem \
   -out secrets/utilityservice-server-cert.pem \
   -subj "/C=CA/ST=Ontario/L=Waterloo/O=OpenText/OU=IT"
   
   openssl pkcs12 -export \
   -out secrets/utilityservice-server-cert.pfx \
   -inkey secrets/utilityservice-server-key.pem \
   -in secrets/utilityservice-server-cert.pem  \
   -passout "pass:${UTILITYSERVICE_SERVER_CERT_PWD}"
   
   echo "Paste the following value in .env > DAST_UTILITY_SSL_CERT_PASSWORD : $UTILITYSERVICE_SERVER_CERT_PWD" 
   ```
4. Set the `DAST_DB_XXX` variables in `.env`
5. Create a service account in the Fortify Software Security Center and set the `SSC_XXX` variables in `.env`
6. Set the `LIM_DB_XXX` variable in `.env`. The Pool must have sensors/scan machine/webinspect licenses and 1 ScanCentral DAST License
7. Update the image tag the `DOCKER_IMAGE_XXX` variables in `.env`
8. Review and edit the Ports in the `.env` and/or `docker-compose.yml` file if needed
   
   **_NOTE:_** Podman is going to complain if you try to set the port to 80 or 443.
9. Run the following command
   ```sh
   podman compose up -d
   ```
   
   You can tail the logs using the following command:
   ```sh
   podman compose logs -f
   ```
10. When the ScanCentral DAST app is up, you can access the Swagger API page using the host and port set in the variable DAST_API_HOST and DAST_API_PORT (from you .env file).
   
      ```sh
      https://<DAST_API_HOST>:<DAST_API_PORT>/swagger/index.html
      ```

11. You can then go to SSC, enable ScanCentral SAST using the ShareSecret defined in the .env file (SCANCENTRAL_CONFIG_SSC_SCANCENTRAL_CTRL_SECRET)
