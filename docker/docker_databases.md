# Docker Database for Local Development

### 1. Download/Install Docker

If Docker is not already installed, follow the platform-specific instructions to install on your system.

Learn More:
- https://docs.docker.com/get-docker/

### 2. Download the Test Data

We don't have any specific test data yet, so start by creating a directory to store your database:

```
(host) $ mkdir /path/to/test_db
```

### 3. Create a Bridge Network

```
(host) $ docker network create --driver=bridge db-net
```

Learn More:
  - Networking Overview: https://docs.docker.com/network/
  - Bridge Network Tutorial: https://docs.docker.com/network/network-tutorial-standalone/
  - Using Bridge Networks: https://docs.docker.com/network/bridge/

### 4. Run the DB Server

Here, we use the MariaDB Container, specifying either our existing database or an empty directory:

```
(host) $ docker run --name db-server -v /path/to/test_db:/var/lib/mysql --network db-net -d mariadb
```

Note: If starting from an empty test_data folder, set root password: `-e MYSQL_ROOT_PASSWORD=secret_pass`

Learn More:
- MariaDB Container Documentation: https://hub.docker.com/_/mariadb
- MariaDB General Docker Documentation: https://mariadb.com/kb/en/installing-and-using-mariadb-via-docker/

### 5. Connect to the DB Server

You can execute an interactive shell on the server container,

```
(host) $ docker exec -it db-server bash
root@zyx:/# 
```

If you want to install any utilities, you'll need to first update `apt`:

```
root@zyx:/# apt update
```

### 6. Create a DB Client

You can create another container on the same network to serve as a database client for executing queries, etc.

```
(host) $ docker run --name db-client --network db-net -it --rm mariadb mysql -hdb-server -uroot -p
```

You will be prompted for the root password. After authenticating, you will be presented with the DB client prompt.
When you leave this container it will be deleted (`--rm`), but the db-server will not be affected.

If you want to keep the client running for use later, you can escape using the sequence `Ctrl-p` `Ctrl-q`. 

To re-attach the client,

```
(host) $ docker attach db-client
MariaDB [(none)]>
```

Learn More:
- Docker Attach: https://docs.docker.com/engine/reference/commandline/attach/

### 7. Create a DB Admin Panel.

If desired, you can create another container that will serve as an admin tool for the database.

```
(host) $ docker run --name db-admin --network db-net -p 8080:8080 -d adminer
```

You can now navigate to the admin panel at `localhost:8080`, and connect with the following settings:
- System=MySQL
- Server=db-server
- Username=root
- Password=(set above)
- Database=(leave blank)

Learn More:
- Adminer Container Docs: https://hub.docker.com/_/adminer/
