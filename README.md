# MongoDB Clustering

This is a quick and dirty breakdown of setting up a MongoDB cluster using Docker and Docker-Compose for your GNU/Linux environments. It should still apply to any environment that's running Docker/Compose but I'm not going to guarantee that the information remains evergreen for those environments, strictly for the GNU/Linux ones.

## Using Docker

1. Install Docker
    - I won't go into detail about how to install Docker since that's something that should be covered as a base knowledge before attempting to set up a cluster of mongo replicasets. Instead, follow the remainder of the steps only if you understand the basics of Docker.

2. Create a Docker Network
    
```bash
docker network create mongo-cluster
```

3. Run MongoDB Replica Set Containers

```bash
docker run -d --name mongo1 -p 27017:27017 --net mongo-cluster mongo:latest --replSet rs0
docker run --name mongo2 -d --net mongo-cluster mongo:latest --replSet rs0
docker run --name mongo3 -d --net mongo-cluster mongo:latest --replSet rs0
```

4. Initialize the Replica Set Configuration

```bash
docker exec -it mongo1 mongosh
```
Once you've exec'd into one of the mongo cluster nodes...

```bash
rs.initiate({ _id: "rs0", members: [{ _id: 0, host: "mongo1:27017" }, { _id: 1, host: "mongo2:27017" }, { _id: 2, host: "mongo3:27017" }] })
```

5. Verify the Replica Set Status

```bash
rs.status()
```

## Using Docker-Compose

