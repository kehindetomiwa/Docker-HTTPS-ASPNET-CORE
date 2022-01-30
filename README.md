# ASPNET Core on Docker with HTTPS

## For devlopement environment
###  Configure projects with user secrets
These instructions assume that your project is configured for [application secrets](https://docs.microsoft.com/aspnet/core/security/app-secrets). The primary requirement is a [UserSecretsId](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/aspnetapp/aspnetapp.csproj#L5) element in your project file. 

### macos

```console
cd <project dir>
```

Generate cert and configure local machine:

```console
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p crypticpassword
dotnet dev-certs https --trust
```

> Note: The certificate name, in this case *aspnetapp*.pfx must match the project assembly name.

> Note: `crypticpassword` is used as a stand-in for a password of your own choosing.

Configure application secrets, for the certificate:

```console
dotnet user-secrets -p aspnetapp/aspnetapp.csproj set "Kestrel:Certificates:Development:Password" "crypticpassword"
```
> Note: the flag -p aspnetapp/aspnetapp.csproj is not needed if you are running the command from the project directory

Build a container image:

```console
docker build --pull -t aspnetapp .
```

run the container

```console
docker run -d -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_ENVIRONMENT=Development -v ${HOME}/.microsoft/usersecrets/:/root/.microsoft/usersecrets -v ${HOME}/.aspnet/https:/root/.aspnet/https/ aspnetapp
```

> Note: you can avoid the long commang line by creating a docker compose file (see docker-compose.yaml)



if you decide to use a docker compose file 

Build image
```console
docker-compose up --build
```

Build Image and Run container
```console
docker-compose up --build
```

