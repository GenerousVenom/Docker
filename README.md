# KM container deployment

## Document overview
- You can visit [this website](https://www.phpkb.com/) to know more about PHPKB\
- User can use VM and run container runtime to deploy KM container or use some pubilic container providers such as Azure, AWS, GCP, ...\
- In this document will help you deploy KM container in a Virtual Machine in VMware using Docker as a container run time. Of cource, it will require more performance because of more layers deployment, but you can leverage this instruction to deploy in physical device or custom it to run on public cloud providers
<p align="center">
  <img src="Image\Layer.png" alt="Image Description">
</p>

## Preparing Virtual Machine in VMware
Install CentOS 8\
Download and start docker runtime service for CentOS [here](https://docs.docker.com/engine/install/centos/)\
Some relative folders:
- "https" folder: Include Docker-compose file to run docker and KM source code
  - "env" floder: Include enviroment variables for Mariadb setup such as root password and database name
  - "km-db-https" folder: Include data which has been created by KM's user
  - "km-grafana" folder: Grafana setup information
    - "config" folder: Grafana configuration infomation such as smtp, template,...
    - "data" folder: Grafana data such as plugins,...
  - "phpkbv9-https" folder: KM's source code
  - “000-default.conf” file: Apache setup for port 80 (http) and port 443 (https) and its certifications
  - “docker-compose-km-https.yml” file: Docker's service requirements
  - "Dockerfile-https" file: Create Apache image
  - “uploads.ini” file: Expand upload file quotas
- “phpkbv9.rar” file: Include encrypted KM source code
- "vi.php" file: Vietnam language defination base on English version
<p align="center">
  <img src="Image\Folder.png" alt="Image Description">
</p>

## KM Container Installation
<p align="center">
  <img src="Image\Flow.png" alt="Image Description">
</p>

1. Extract phpkbv9.zip file\
After extracting phpkbv9.zip file by given password, copy this extracted file to https folder
2. KM's data preparation *(you can pass this step if you install new KM)*
- Copy some old folders to the extracted KM including:
  - phpkbv9/admin/languages/
  -	phpkbv9/admin/backups/
  -	phpkbv9/admin/include/
  -	phpkbv9/assets/
  -	phpkbv9/sitemap/
  -	phpkbv9/uploads/
3. Setup Grafana *(you can pass this step if you install new Grafana)*
- Copy all folder in current Grafana follow this link /var/lib/Grafana to km-grafana/data folder (include plugins, alerting and Grafana template)
- Copy configuration file from current Grafana in /etc/grafana/grafana.ini to km-grafana/config/grafana.ini (mail server,...)
4. Run docker-compose file
- Using this command: *docker-compose -f `<`dockerfile name`>` up -d –build* to start all services
5. PHPKB setup
- Add license key in “license_key.txt” file
  <p align="center">
    <img src="Image\Step1.png" alt="Image Description">
  </p>

- Add some required informations
  - MySQL Server: 
  - MySQL Username:
  - MySQL Password:
  - MySQL Database:
  <p align="center">
    <img src="Image\Step2.png" alt="Image Description">
  </p>

- Complete installation
  <p align="center">
    <img src="Image\Step3.png" alt="Image Description">
  </p>

6. Restore MariaDB *(you can pass this step if you install new KM)*
- Stop all started services in docker-compose file: *docker-compose -f `<`dockerfile name`>` down*
- Delete all files in /km-db-https during installation
- Copy data file in from current MariaDB /var/lib/mysql to /km-db-https file (include databases, tables, ...)
- Start all services from docker-compose file again: *docker-compose -f `<`dockerfile name`>` up -d*
- If you setup LDAP in the previous KM system, all users from the old LDAP will present in the new system, you have to login to MariaDB to delete all of them and reconfigure the new LDAP system

## Backup KM Container
1. External Data Storages
  <p align="center">
    <img src="Image\Data Storages.png" alt="Image Description">
  </p>

2. KM container storage
- Bind Mounts:
  - KM articles and installation file
  - Mariadb database (articles' database)
  - Mariadb, Grafana and Influxdb configuration file
- Named volume: 
  - Influxdb database

3. Folders need to backup
- KM articles
  - phpkbv9/admin/languages/
  -	phpkbv9/admin/backups/
  -	phpkbv9/admin/include/
  -	phpkbv9/assets/
  -	phpkbv9/sitemap/
  -	phpkbv9/uploads/
- System configurations
  - influxdb/influxdb.conf
  -	km-grafana
- Databases:
  -	volume influxdata
  -	km-db-https

## KM container Architecture
<p align="center">
  <img src="Image\Architecture.png" alt="Image Description">
</p>