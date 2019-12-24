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
7. **docker inspect [containerid]** will provide details of running container.
8. **docker tag 8c76f4fd7487 heemabhatt/netcorewebapp:v2** to tag with version where 8c76f4fd7487 is image name.
9. **docker push heemabhatt/netcorewebapp** to push image to docker hub. it should reflect here: https://hub.docker.com/repositories


## Kubernetes Orchestration with same example:

# To Enable Kubernetes

1. from docker desktop setting (Increased CPU ,memory sizes, enabled kubernetes)
2. Download kubectl.exe 
3. run command kubectl,  kubectl get nodes ,  on powershell admin. should show list of commands and nodes. this will check successfule installation

# To Enable ui :

1. run command **kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml**

# To start ui:

1. Run command **kubectl proxy**
2. Open http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/overview?namespace=default on browser, it should ask for authentication. 
3. Select token

# To generate token:

1. Run command **$TOKEN=((kubectl -n kube-system describe secret default | Select-String "token:") -split " +")[1]**
2. Run command **kubectl config set-credentials docker-for-desktop --token="${TOKEN}"**
3. copy token from C:\Users\hbhatt\.kube\config file and put it in ui. 

# To setup menifest 

1. create yaml as k8deploy.yml , provide replica count, metadata of app, port , node port (this is used to run app locally like http://localhost:30069/ where 30069 is nodeport), version details
2. use command to create menifest : ** kuebctl create -f .\k8deploy.yml**
3. to apply update in yml : **kubectl apply -f .\k8deploy.yml**
4. to apply changes in application,  **docker build -t heemabhatt/netcorewebapp .** and then **kubectl delete -f .\k8deploy.yml** and **kubectl apply -f .\k8deploy.yml**


# AKS Deployment

1. Create AKS Cluster with name demok8cluster and resource group K8demoRg
2. Create aksdeploy.yml and upload on Azure bash
3. once deployment is successful, run following commands: 
az aks get-credentials --resource-group K8demoRg --name demok8cluster

kubectl get nodes

kubectl apply -f aksdeploy.yaml

kubectl get service netcorewebappservice --watch

kubectl get pods

# to open dashboard 

kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard

az aks browse --resource-group K8demoRg --name demok8cluster
