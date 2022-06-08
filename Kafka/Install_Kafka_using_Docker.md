# **INSTALL KAFKA USING DOCKER**

You will need two Docker images to get Kafka running:
- bitmani/zookeeper:3.8
- bitmani/kafka:3.2

First, you use a docker-compose.yml to download the images.

Now, you can now open up a Terminal and navigate to a folder where you saved docker-compose.yml file. 

    cd C:\Users\maria.puche\Desktop\Docker

Execute the following command to pull the images and create containers:

    docker-compose -f docker-compose.yml up -d

The -d means both Zookeeper and Kafka will run in the background, so you’ll have access to the Terminal after they start.

You can use the docker ps command to verify both are running.

    docker ps

## **Conect to Kafka shell**

Once Zookeeper and Kafka containers are running, you can execute the following Terminal command to start a Kafka shell:

    docker exec -it bloque1-kafka-1 /bin/sh

If you've decided to name it differently in the docker-compose.yml file, just replace kafka with the value of container_name.      

Now you have everything needed to create your first Kafka topic!