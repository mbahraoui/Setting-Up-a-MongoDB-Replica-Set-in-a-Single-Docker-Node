# Setting Up a MongoDB Replica Set in a Single Docker Node

## Part 1: Setting Up a MongoDB Replica Set with Docker without using docker-compose

This README provides instructions on how to set up a MongoDB replica set using Docker containers. A replica set is a group of MongoDB instances that work together to provide high availability and data redundancy.


### Step 1: Create a Docker Network

Create a Docker network to enable communication between the MongoDB containers.

```bash
docker network create mongoCluster
```

### Step 2: Start MongoDB Instances

Start the MongoDB instances using Docker containers. Replace 27018 and 27019 with desired ports for additional nodes.

```bash
docker run -d --rm -p 27017:27017 --name mongo1 --network mongoCluster mongo mongod --replSet myReplicaSet --bind_ip localhost,mongo1
docker run -d --rm -p 27018:27017 --name mongo2 --network mongoCluster mongo mongod --replSet myReplicaSet --bind_ip localhost,mongo2
docker run -d --rm -p 27019:27017 --name mongo3 --network mongoCluster mongo mongod --replSet myReplicaSet --bind_ip localhost,mongo3

```

### Step 3: Initiate the Replica Set

Initiate the MongoDB replica set by connecting to the primary node (mongo1) using the MongoDB shell (mongosh).

```bash
docker exec -it mongo1 mongosh --eval "rs.initiate({
_id: \"myReplicaSet\",
members: [
{_id: 0, host: \"mongo1\"},
{_id: 1, host: \"mongo2\"},
{_id: 2, host: \"mongo3\"}
]
})"

```

### Step 4: Test and Verify the Replica Set

You can test and verify the replica set's status by connecting to the MongoDB shell on the primary node (mongo1).

```bash
docker exec -it mongo1 mongosh --eval "rs.status()"

```

This command will provide information about the status and configuration of the replica set.

## Part 2: MongoDB Replica Set Configuration using Docker Compose

This section covers setting up a MongoDB replica set using Docker Compose, simplifying the process and allowing for easier management.

### Step 1: Create a Directory and Compose File

Create a directory for your MongoDB replica set configuration. Inside this directory, create a file named `docker-compose.yml`.

### Step 2: Configure Docker Compose

Copy and paste the following content into your `docker-compose.yml` file:

```yaml
version: '3.7'
services:
  mongo1:
    image: mongo
    container_name: mongo1
    command: mongod --replSet myReplicaSet --bind_ip_all
    ports:
      - "27017:27017"
    networks:
      - mongoCluster
    hostname: mongo1
    volumes:
      - mongo1-data:/data/db

  mongo2:
    image: mongo
    container_name: mongo2
    command: mongod --replSet myReplicaSet --bind_ip_all
    ports:
      - "27018:27017"
    networks:
      - mongoCluster
    hostname: mongo2
    volumes:
      - mongo2-data:/data/db

  mongo3:
    image: mongo
    container_name: mongo3
    command: mongod --replSet myReplicaSet --bind_ip_all
    ports:
      - "27019:27017"
    networks:
      - mongoCluster
    hostname: mongo3
    volumes:
      - mongo3-data:/data/db

networks:
  mongoCluster:
    driver: bridge

volumes:
  mongo1-data:
  mongo2-data:
  mongo3-data:

```

### Step 3: Start the Containers

Open a terminal in the directory where your docker-compose.yml file is located and run the following command to start the MongoDB containers:

```bash
docker-compose up -d
```

### Step 4: Initialize the Replica Set

Once the containers are up and running, initiate the replica set using the mongosh shell. Run the following command:

```bash
docker exec -it mongo1 mongosh --eval "rs.initiate({
_id: \"myReplicaSet\",
members: [
{_id: 0, host: \"mongo1\"},
{_id: 1, host: \"mongo2\"},
{_id: 2, host: \"mongo3\"}
]
})"
```
### Step 5: Access the Replica Set

You can access the replica set using the MongoDB shell by connecting to the primary node. Run the following command:

```bash
docker exec -it mongo1 mongosh
```

### Step 6: Stopping and Cleanup

To stop and remove the containers, you can run:

```bash
docker-compose down
```
