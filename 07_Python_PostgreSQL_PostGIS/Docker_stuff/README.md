# README

## Table of Contents
- [Table of Contents](#table-of-contents)
- [Run the Docker container](#run-docker-containers)
- [pgadmin settings](#pgadmin-settings)
- [Import OSM data](#import-osm-data)
- [Copy example SQL files to pgAdmin](#copy-example-sql-files-to-pgadmin)


## Run Docker containers

```bash
# Create Docker image and run Docker container
VS Code -> cd into the folder Docker_stuff

Search the file 'docker-compose.yml' -> right click -> Compose Up

# If the container is running, access pgAdmin in browser
http://127.0.0.1:5050
```

## pgAdmin settings

pgAdmin does not provide a way to automatically register a server. Server registration in pgAdmin is a manual process that needs to be done through the pgAdmin UI. The following description shows how.

```bash
# Server registration in pgAdmin (change user name and password if required):
    Servers -> right click -> register Server

    Use the 'General' tab
    --> Name: PostgreSQL 16

    Use the 'Connection' tab
        --> Host name/address: db
        --> Port: 5432
        --> Username: postgres
        --> Password: geheim

    --> All other settings: use default or leave empty
```

## Import OSM Data

In VS Code -> Terminal, use the following Docker commands to insert tables with OpenStreetMap data.

```bash
# Show running containers
docker ps

# Show available data bases
docker exec -it postgis_container psql -U postgres -c "\l"

# Run osm2pgsql commands to fill up OpenStreetMap tables
# Open bash
docker exec -it postgis_container bash

# Run the following code in bash (change user name and password if required)
PGPASSWORD=geheim osm2pgsql -c -d osm_switzerland -U postgres -H db -P 5432 -S /usr/bin/default.style /tmp/switzerland-latest.osm.pbf

# Exit bash
exit

# Show available tables in the database 'osm_switzerland'
docker exec -it postgis_container psql -U postgres -d osm_switzerland -c "\dt;"

# quit psql
q
```

## Copy example SQL files to pgAdmin

```bashs
# cd into Docker_stuff folder (look for the folder 'SQL')

# Get your storage location
docker exec -it pgadmin_container ls /var/lib/pgadmin/storage

# Copy folder with SQL exanples to running pgadmin_container (replace LOCATION with your storage location)
docker cp SQL/ pgadmin_container:/var/lib/pgadmin/storage/LOCATION/

# Provide permission to copied folder / files (replace LOCATION with your storage location)
docker exec -u root pgadmin_container chmod -R 777 /var/lib/pgadmin/storage/LOCATION/
```