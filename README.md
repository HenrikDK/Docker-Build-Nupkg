# Using a docker container to build a nuget package

Purpose of this repository is to demonstrate how docker can be used to build a NuGet package, that can then be uploaded to an artifact repository.

The intended use case is to remove dependency on specific build tools and allow a portable build of a class library or application.

## The Setup
The solution works by combining Dockers BuildKit features that allow files to be copied out of the container

  1. A docker file that doesn't build a new docker image but instead ends with a copy statement
```
# Copy everything and build
FROM mcr.microsoft.com/dotnet/sdk:5.0-alpine AS build-env
WORKDIR /app

COPY . ./
RUN dotnet pack -c Debug -o out

FROM scratch AS export-stage

COPY --from=build-env /app/out /
```

  2. The docker file has to be called with an out put parameter
```
docker build -o out . 
```

This will place the .nupkg in a sub folder inside the project.

## Sources
With inspiration from: 

https://reece.tech/posts/extracting-files-multi-stage-docker/

https://alexei-led.github.io/post/docker_builder_pattern/

https://github.com/dotnet/sdk/issues/10335
