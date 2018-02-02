# Build
## Create Docker images
```
docker build -f .\ContosoUniversity\Dockerfile -t christianhxc/contosouniversity:latest -t christianhxc/contosouniversity:1.0 .\ContosoUniversity
```

```
docker push christianhxc/contosouniversity:latest
```

```
docker push christianhxc/contosouniversity:1.0
```

## Create docker image with code to run migrations
```
docker build -f .\ContosoUniversity\Dockerfile --target "build" -t christianhxc/contosouniversity:latest-build .\ContosoUniversity
```

```
docker push christianhxc/contosouniversity:1.0-build
```

# Deployment (Any Environment)
## Deploy image with mssql indicating the endpoint for the DB
```
kubectl apply -f .\mssql-deployment.yaml
```

## Run migration after everything is running (local)
```
docker run -e DB_ENDPOINT='database' --network=$(docker inspect --format='{{range .NetworkSettings.Networks}}{{.NetworkID}}{{end}}' contosouniversitydb) --entrypoint "dotnet" christianhxc/contosouniversity:latest-build ef database update
```

## Run migration after everything is running (remotely)
```
docker run -e DB_ENDPOINT='40.121.194.106' --entrypoint "dotnet" christianhxc/contosouniversity:latest-build ef database update
```

## Deploy image with web app
```
kubectl apply -f .\webapp-deployment.yaml
```

# Clean-up (or restart)
```
kubectl delete pods -l app=contosouniversitydb
```

```
kubectl delete pods -l app=contosouniversityweb
```