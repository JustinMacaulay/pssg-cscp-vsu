ARG BUILD_ID

# Retrieve and set base image layer
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
ARG BUILD_ID
WORKDIR /app

# Create build image for runtime
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
COPY . .

# Install Node (required to install Angular)
RUN apt-get update -yq &&  \
  apt-get upgrade -yq &&   \
  apt-get install -yq curl
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt-get install -yq nodejs=12.22.12-deb-1nodesource1

# Restore as distinct layers
RUN dotnet restore "./vsu-app/vsu-app.csproj"
RUN dotnet build "vsu-app/vsu-app.csproj" -c Release -o /app/build

# Create publish image layer
FROM build AS publish
ARG BUILD_ID
COPY . .

# Build and publish a release
RUN dotnet publish "vsu-app/vsu-app.csproj" -c Release -o /app/publish

# Build runtime image
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
EXPOSE 8080
ENV ASPNETCORE_URLS=http://*:8080
ENTRYPOINT ["dotnet", "vsu-app.dll"]