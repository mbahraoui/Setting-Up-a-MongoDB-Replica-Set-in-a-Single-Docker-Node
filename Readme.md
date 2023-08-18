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

