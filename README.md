# DockerExample

# Test Docker Setup

  docker --version,
  
  docker run hello-world,
  
  docker images ls



# Add Docker file with following:

FROM microsoft/dotnet:2.2-aspnetcore-runtime AS base

WORKDIR /app

EXPOSE 80

EXPOSE 443

FROM microsoft/dotnet:2.2-sdk AS build

WORKDIR /src

COPY ["NetCoreWebApp/NetCoreWebApp.csproj", "NetCoreWebApp/"]

RUN dotnet restore "NetCoreWebApp/NetCoreWebApp.csproj"

COPY . .

WORKDIR "/src/NetCoreWebApp"

RUN dotnet build "NetCoreWebApp.csproj" -c Release -o /app


FROM build AS publish

RUN dotnet publish "NetCoreWebApp.csproj" -c Release -o /app


FROM base AS final

WORKDIR /app

COPY --from=publish /app .

ENTRYPOINT ["dotnet", "NetCoreWebApp.dll"]

##Update "NetCoreWebApp" with your project dll name


# Commands to Run docker images and Net core web app

1. Redirect to .csproj location
2. try **dotnet run**, it should run successfully
3.  **docker build -t heemabhatt/netcorewebapp .**   This will create web app with tag name
4.  **docker run -p 7001:80 heemabhatt/netcorewebapp** This will run web app from container on port 7001. Any port can be specified in stead of 7001. 
5. **docker images** will give list of all running instances
6. **docker ps** will give list of containers with container id.
7. **docker inspect [containerid]** will provide details of running container
