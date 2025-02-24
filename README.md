# Fortify on Podman Deployment
## ❓ Description

This repo contains various components for the deployment of ScanCentral DAST and Software Security Center, among others.

Some basic information about the operation of Podman (compared to Docker Compose)

1. Adding :z to each volumes in docker-compose.yml
2. Run a `sudo chmod -R 777 <working directory>` against all needed directories
3. Checking that the .env variables doesn't have in-line comments
4. Make sure that the ports in the container are not 80 or 443, but something like 8080 and 8443
