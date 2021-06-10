FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-buster-slim AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/core/sdk:3.1-buster AS build
WORKDIR /src
COPY ["NAME-OF-YOUR-PROJECT/NAME-OF-YOUR-PROJECT.csproj", "NAME-OF-YOUR-PROJECT/"]

COPY . .
WORKDIR "/src/NAME-OF-YOUR-PROJECT"

ARG FEED_URL
ARG PAT
ARG USR
RUN apt-get update
# RUN ./installcredprovider.sh
RUN apt-get install -y wget
# download and install latest credential provider. Not required after https://github.com/dotnet/dotnet-docker/issues/878
RUN wget -qO- https://aka.ms/install-artifacts-credprovider.sh | bash



# Optional: Sometimes the http client hangs because of a .NEt issue. Setting this in dockerfile helps 
ENV DOTNET_SYSTEM_NET_HTTP_USESOCKETSHTTPHANDLER=0
# Environment variable to enable seesion token cache. More on this here: https://github.com/Microsoft/artifacts-credprovider#help
ENV NUGET_CREDENTIALPROVIDER_SESSIONTOKENCACHE_ENABLED true

# Environment variable for adding endpoint credentials. More on this here: https://github.com/Microsoft/artifacts-credprovider#help
# Add "FEED_URL" AND "PAT" using --build-arg in docker build step. "endpointCredentials" field is an array, you can add multiple endpoint configurations.
# Make sure that you *do not* hard code the "PAT" here. That is a sensitive information and must not be checked in.

RUN dotnet restore 
RUN dotnet build "NAME-OF-YOUR-PROJECT.csproj" -c Release -o /app/build --runtime linux-x64

FROM build AS publish
RUN dotnet publish "NAME-OF-YOUR-PROJECT.csproj" -c Release -o /app/publish --runtime linux-x64

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "NAME-OF-YOUR-PROJECT.dll"]