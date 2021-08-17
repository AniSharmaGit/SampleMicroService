#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

#Docker build process start in a kind of empty container.
#Vieo to explain the docker file command https://www.youtube.com/watch?v=f0lMGPB10bM 

# Line9 : We are asking docker that we need asp.net run time to run application.
# Line10 : Creating a working directory app
# Line11 : exposing port.
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS base
WORKDIR /app
EXPOSE 80
# Line20 : We are asking docker that we need sdk to build application.
# Line21 : Creating a working directory src
# Line22 : coping csproj file to folder "Services/Basket/Basket.API/" in docker .
# Line23 : coping csproj file to folder "BuildingBlocks/EventBus.Messages/" in docker dependent project .
# Line24 : run restore command to resolve all dependancy.
# Line25 : now copy all other files from computer to docker.
# Line26 : set working directory "/src/Services/Basket/Basket.API".
# Line27 : build project in Release mode and output in /app/build folder".
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /src
COPY ["Services/Basket/Basket.API/Basket.API.csproj", "Services/Basket/Basket.API/"]
COPY ["BuildingBlocks/EventBus.Messages/EventBus.Messages.csproj", "BuildingBlocks/EventBus.Messages/"]
RUN dotnet restore "Services/Basket/Basket.API/Basket.API.csproj"
COPY . .
WORKDIR "/src/Services/Basket/Basket.API"
RUN dotnet build "Basket.API.csproj" -c Release -o /app/build

# Line30 : take output of the previous steps (build)
# Line31 : take the previous output and publish it into /app/publish folder
FROM build AS publish
RUN dotnet publish "Basket.API.csproj" -c Release -o /app/publish

# Line30 : take output of the previous steps 9 (base we want to use asp runtime in final docker)
# Line31 : take output previous steps (publish) and copy it /ap/publish
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Basket.API.dll"]