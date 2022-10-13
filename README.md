# Install Redash in EC2
The document will discuss in detail the steps to install Redash in EC2 instances. The setup can be expanded to have an Ec2 cluster with a load balancer. 

1. Prerequsites
2. Launch an EC2 instance
3. Install docker and docker compose
4. Start redash
5. Setup metadata database

### Prerequsites
1. A postgres database that can be accessed from the EC2 instance. This database will be used by redash to save the metadata. This would help when we scale horizontally by adding more EC2 instances to the fleet. Change the following line in the `docker-compose.yml` with the correct values. 
```
REDASH_DATABASE_URL: "postgresql://username:password@hostname/redash"
```
2. A redis cache that can be accessed from the EC2 instance. Chnage the corresponding lines in `docker-compose.yml` with the correct values.

### Launch an EC2 instance
Use the following settings when creating the EC2 instance:
1. **Disk**: 40GB
2. **Instance Type**: t3.medium
3. **Port**: Open port 80 accessible to all

### Install docker and docker compose
Run the following commands in the same order:
```
sudo apt-get update
sudo apt-get install docker.io -y
sudo groupadd docker
sudo gpasswd -a $USER docker
newgrp docker
docker ps -a // Run this command to see if there is any permission error or not
sudo apt-get install docker-compose
```
### Start redash
1. Copy the docker compose file to EC2 machine
2. Run the following command to start Redash
```
docker-compose up -d
```

### Setup metadata database
The following steps are only needed when you run redash with a fresh metadata db. 
1. Run `docker ps -a`. You should see the services up and running. 
2. Run `docker exec -it redash_server_1 bash` to login to the docker container. 
3. Run `python /app/manage.py database create_tables` to create the metadata schema. 

You are all set. Access redash with the ip of the machine. 

