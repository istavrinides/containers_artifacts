# Initialize SQL Server locally
## Run SQL Server
`docker run --network openhack --name dbsrv -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=Pass@word123!!' -p 1433:1433 -d mcr.microsoft.com/mssql/server:2017-latest`

## Run CREATE DATABASE command
`docker exec dbsrv /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Pass@word123!!' -Q "CREATE DATABASE mydrivingDB"`

## Run data load container
`docker run --network openhack -e SQLFQDN=dbsrv -e SQLUSER=sa -e SQLPASS='Pass@word123!!' -e SQLDB=mydrivingDB openhack/data-load:v1`

# Build the images
*Change directory to Java: **/src/user-java***

`docker build . -f ../../dockerfiles/Dockerfile_0 -t java:latest`

*Change directory to TripViewer: **/src/tripviewer***

`docker build . -f ../../dockerfiles/Dockerfile_1 -t tripviewer:latest`

*Change directory to Userprofile: **/srv/userprofile***

`docker build . -f ../../dockerfiles/Dockerfile_2 -t userprofile:latest`

*Change directory to POI: **/src/poi***

`docker build . -f ../../dockerfiles/Dockerfile_3 -t poi:latest`

*Change directory to Trips: **/src/trips***
`docker build . -f ../../dockerfiles/Dockerfile_4 -t trips:latest`

# Run the containers
## Java container
`docker run --network openhack -e 'PORT=8080' -e 'SQL_USER=sa' -e 'SQL_PASSWORD=Pass@word123!!' -e 'SQL_SERVER=dbsrv' -p 8080:8080 --name java java:latest`

## Trip Viewer container
`docker run --network openhack -e 'TRIPS_API_ENDPOINT=http://trips' -e 'USERPROFILE_API_ENDPOINT=http://userprofile' --name tripviewer tripviewer:latest`

## User profile container
`docker run --network openhack -e 'PORT=8081' -e 'SQL_USER=sa' -e 'SQL_PASSWORD=Pass@word123!!' -e 'SQL_SERVER=dbsrv' -p 8081:8081 --name userprofile userprofile:latest`

## POI container
`docker run --network openhack -e 'WEB_PORT=8082' -e 'WEB_SERVER_BASE_URI=http://poi' -e 'ASPNETCORE_ENVIRONMENT=Local' -e 'SQL_USER=sa' -e 'SQL_PASSWORD=Pass@word123!!' -e 'SQL_SERVER=dbsrv' -p 8082:8082 --name poi poi:latest`

## Trips container
`docker run --network openhack -e 'PORT=8083' -e 'SQL_USER=sa' -e 'SQL_PASSWORD=Pass@word123!!' -e 'SQL_SERVER=dbsrv' -e 'OPENAPI_DOCS_URI=http://changeme' -p 8083:8083 --name trips trips:latest`