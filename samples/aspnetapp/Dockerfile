# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY *.Config .
COPY aspnetapp/*.csproj ./aspnetapp/
ARG FEED_ACCESSTOKEN
RUN dotnet nuget locals all --clear
RUN dotnet nuget update source jeffkl-work --source https://pkgs.dev.azure.com/jeffkl-work/_packaging/jeffkl-work/nuget/v3/index.json --username ANYTHING --password ${FEED_ACCESSTOKEN} --configfile NuGet.Config --store-password-in-clear-text
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:6.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
