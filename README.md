# Exhaustive-Open-Route-Service-Docker-Guide
This is an exhaustive guide to successfully setup a local ORS server using docker.

# Get the docker-compose
First and foremost, we need the docker-compose file, so navigate to the folder of your choice, open a bash terminal and run:
```
curl -L -o docker-compose.yml https://github.com/GIScience/openrouteservice/releases/download/v8.0.0/docker-compose.yml
```

# Get your osm.pbf file
I download this ```spain-latest.osm.pbf``` from https://download.geofabrik.de/europe/spain.html but feel free to download the one you need. Download it wherever, we will be moving it soon.

# Modify the docker-compose file
Now we are going to make some adjustments to the file.

## Enviroment
Ctfl + F -> enviroment and there paste this
```
environment:
      REBUILD_GRAPHS: "true"
      ors.engine.source_file: /home/ors/files/spain-latest.osm.pbf
      ors.engine.graphs_root_path: /home/ors/graphs
      ors.engine.elevation.cache_path: /home/ors/elevation_cache
      ors.engine.profiles.car.enabled: "true"

      CONTAINER_LOG_LEVEL: INFO # Log level for the container. Possible values: DEBUG, INFO, WARNING, ERROR, CRITICAL
```
ors.engine.source_file: /home/ors/files/spain-latest.osm.pbf HERE WRITE THE NAME OF YOUR OSM.PBF FILE, IGNORE THE PATH FOR NOW.
REBUILD_GRAPHS: "true" is key.

## Build
Ctrl + F and delete this 
```
build:
  context: ./
```

# Run docker compose up 
This will start the container and you will see something like this ```INFO [graphhopper] - start creating graphs from file spain-latest.osm.pbf``` somewhere. Fine. Now an error will shown because the file is ofc not here since the files folder were just created.

# Copy the file
ors-docker/files/yours.osm.pbf
easy

# Restart
Run 
```
rm -rf ors-docker/graphs/*
```
To remove the corrupted graphs

```
docker compose down -v 
docker compose up --build

```
To restart the docker container.

# WAIT
NOW YOU HAVE TO WAIT A LOOOOOT OF TIME (from 20 mins to hours depending of your system) so ORS can build the graphs up.

# ERROR JAVA HEAP
This will shown up if you are not using enough RAM. Modify the docker-compose:
```
XMS: 6g # start RAM assigned to java
XMX: 8g # max RAM assigned to java. Rule of Thumb: <PBF-size> * <profiles> * 2
```
this is also in enviroment by the way.

# How do I know it is done?

You can ping http://localhost:8080/ors/v2/health or take a look at the console and see if something like this appears:
```
ors-app  | INFO [graphhopper.GraphHopper] - Loaded graph with ... nodes and ... edges
ors-app  | INFO [routing.RoutingProfileManager] - Routing profiles initialized
ors-app  | INFO [openrouteservice.Main] - Started application in XXX seconds
ors-app  | INFO [tomcat] - Server startup in XXXX ms

or

ors-app  | INFO [server.ServerConnector] - Started @8080

```

# YOU ARE WELCOME!!
