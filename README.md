# sql-docker-ad

This repo documents the steps one must follow to allow SQL Server running in a Linux Docker ocontainer hosted on Ubuntu to allow authorization and login by Azure Active Directory Users. 

Required attributes:

1) Microsoft SQL Server 2019 from standard Microsoft Dockerhub repo
2) Running in Linux Docker container (whatever is natively used by Microsoft Docker image)
3) Azure resident host VM running Ubuntu 20.04
4) Transpara.com Azure Active Directory Domain Users authenticated to SQL in Docker natively.

Not required (but OK if true):

5) Host machine joined to Azure AD Domain
6) Docker container joined to Azure AD Domain

