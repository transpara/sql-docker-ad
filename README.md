# sql-docker-ad

This repo documents the steps one must follow to allow SQL Server running in a Linux Docker ocontainer hosted on Ubuntu to allow authorization and login by Azure Active Directory Users. 

## Required attributes:

1) Microsoft SQL Server 2019 from standard Microsoft Dockerhub repo
2) Running in Linux Docker container (whatever is natively used by Microsoft Docker image)
3) Azure resident host VM running Ubuntu 20.04
4) Transpara.com Azure Active Directory Domain Users authenticated to SQL in Docker natively.

## Not required (but OK if true):

5) Host machine joined to Azure AD Domain
6) Docker container joined to Azure AD Domain

## Steps followed:

### 1) Created Ubuntu 20.04 host machine: ZLUBE2V-SQL

### 2) Join this machine to the Transpara.com Domain (optopnal but we did this)
#### Followed instructions at: https://docs.microsoft.com/en-us/azure/active-directory-domain-services/join-ubuntu-linux-vm
Resulted in this behavior (all expected)
~$ realm list
transpara.com
type: kerberos
realm-name: TRANSPARA.COM
domain-name: transpara.com
configured: kerberos-member
server-software: active-directory
client-software: sssd
required-package: sssd-tools
required-package: sssd
required-package: libnss-sss
required-package: libpam-sss
required-package: adcli
required-package: samba-common-bin
login-formats: %U
login-policy: allow-realm-logins
~$ 

### 3) Created Docker runtime from MSSQL standard 2019 image

Docker run.sh: 
sudo docker run -d -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=BorgGoesLive22" \
   --restart unless-stopped \
   --shm-size 1g \
   -p 1433:1433 \
   --name sql1 \
   --hostname sql1 \
   -v /datadrive/container/sql1:/var/opt/mssql \
   -v /datadrive/container/sql1/data:/var/opt/mssql/data \
   -v /datadrive/container/sql1/log:/var/opt/mssql/log \
   -v /datadrive/container/sql1/backup:/var/opt/mssql/backup \
   -v /datadrive/container/sql1/secrets:/var/opt/mssql/secrets \
   -v /datadrive/container/sql1/krb5.conf:/etc/krb5.conf \
   --env MSSQL_AGENT_ENABLED=True \
   --dns-search transpara.com \
   --dns 10.0.0.4 \
   --add-host adVM.transpara.com:10.0.0.4 \
   --add-host transpara.com:10.0.0.4 \
   --add-host transpara:10.0.0.4 \
   --add-host=host.docker.internal:host-gateway \
   mcr.microsoft.com/mssql/server:2019-latest

### 4) This resulted in a Docker ps like this:

docker ps
CONTAINER ID   IMAGE                                        COMMAND                  CREATED      STATUS      PORTS                                       NAMES
a374bbaaf8f6   mcr.microsoft.com/mssql/server:2019-latest   "/opt/mssql/bin/perm…"   5 days ago   Up 4 days   0.0.0.0:1433->1433/tcp, :::1433->1433/tcp   sql1
    michael.saucier@ZLUBE2V-SQL:~$ 

### 5) Enable AD auth
#### Followed instructions here https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-containers-ad-auth-adutil-tutorial?view=sql-server-ver16


