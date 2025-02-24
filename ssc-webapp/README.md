# Software Security Center (SSC)
## ❓ Description

This repo contains the files required for a Podman Fortify Software Security Center deployment.

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
    podman save fortifydocker/ssc-webapp:<tag> -o ssc-webapp.tar
    ```
3. Get this tar file in the destination environment, and load it
    ```
    podman load -i ssc-webapp.tar
    ```

    **_NOTE:_**  This step is not required if you have direct connection to Docker Hub

### 🐳 Deployment

1. Drop `fortify.license` file in `secrets`
2. Create HTTP certificate Password
    ```shell
    HTTP_CERT_PWD="$(openssl rand -base64 32)"
    echo $HTTP_CERT_PWD > secrets/keystore_password
    ```
3. Create HTTP Keystore File
    ```shell
    keytool -genkeypair -keyalg RSA -keysize 2048 \
    -keystore secrets/httpKeystore.jks \
    -alias ssc-server \
    -storepass $HTTP_CERT_PWD \
    -keypass $HTTP_CERT_PWD \
    -dname "C=CA, ST=Ontario, L=Waterloo, O=OpenText, OU=IT"
    ```
4. Copy or rename [ssc.autoconfig.template](ssc.autoconfig.template). Review the file `secrets/ssc.autoconfig`
5. Copy or rename [.env.template](.env.template) to `.env`. Review and edit the `.env` file if needed

    **_NOTE:_** Please make sure to delete all in line comments
6. Review and edit the `docker-compose.yml` file if needed
   
   **_NOTE:_** Podman is going to complain if you try to set the port to 80 or 443.
7. Create a `data` folder and set write permissions to your container
    ```shell
    sudo chmod -R 777 data secrets
    ```
8. Run the following command
   ```sh
   podman compose up -d
   ```
   
   You can tail the logs using the following command:
   ```sh
   podman compose logs -f
   ```
9. When the SSC app is up, access it using the url set in the variable appProperties.host.url (from your `secrets/ssc.autoconfig` file). You can also try https://localhost

### 🌡 Environment variables

| Name             | Description                                                                                                                                                                                                                                                                                                                                                                             | Example                |
|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------|
|HTTP_SERVER_CERTIFICATE_KEYSTORE_FILE| SSC certificate keystore. This is the absolute path to the certificate inside the container. This file should be placed in the [secrets](secrets) folder from your host                                                                                                                                                                                                                 | `/app/secrets/ssc-webapp.pfx`|
|HTTP_SERVER_CERTIFICATE_KEYSTORE_PASSWORD_FILE| Password for the above keystore file. This is the absolute path to a file containing the password, inside the container. This file should be placed in the [secrets](secrets) folder from your host                                                                                                                                                                                     | `/app/secrets/keystore_password`|
|HTTP_SERVER_CERTIFICATE_KEY_PASSWORD_FILE| File storing the password for the key inside the keystore . This is the absolute path to a file containing the password, inside the container. This file should be placed in the [secrets](secrets) folder from your host                                                                                                                                                               | `/app/secrets/keystore_password`|
|COM_FORTIFY_SSC_LICENSEPATH| Fortify license file . This is the absolute path to the license file, inside the container. This file should be placed in the [secrets](secrets) folder from your host                                                                                                                                                                                                                  | `/app/secrets/fortify.license`|
|COM_FORTIFY_SSC_AUTOCONFIGPATH| SSC autoconfig file . This is the absolute path to the file, inside the container. This file should be placed in the [secrets](secrets) folder from your host                                                                                                                                                                                                                           | `/app/secrets/ssc.autoconfig`|
|COM_FORTIFY_SSC_SECRETKEY| This secret key is used by SSC to encrypt some values in its db (ex: ScanCentral SAST Shared Secret). This is the absolute path to the file, inside the container. This file should be placed in the [secrets](secrets) folder from your host. You should use [get-secret-key.sh](get-secret-key.sh) after first startup, then uncomment this env variable, then restart your container | `/app/secrets/ssc.autoconfig`|