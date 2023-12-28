ARG DOTNET_VERSION="8.0-alpine"
FROM mcr.microsoft.com/dotnet/runtime-deps:${DOTNET_VERSION} as base
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:${DOTNET_VERSION} as restorepackages

WORKDIR /src

ARG RUNTIME="linux-musl-x64"
ARG BUILD_CONFIG="Release"
ENV runtime=${RUNTIME}
ENV buildConfig=${BUILD_CONFIG}

ENV INFRA_FOLDER="src/Infrastructure"

COPY ["*.props", ""]
COPY ["src/*.props", "src/"]
COPY ["${INFRA_FOLDER}/*.csproj","${INFRA_FOLDER}/packages.lock.json*", "${INFRA_FOLDER}/" ]

WORKDIR "/src/${INFRA_FOLDER}"

RUN dotnet restore \
  -r $runtime \
  -p:PublishReadyToRun=true \
  -p:PublishSelfContained=true \
  -p:PublishSingleFile=true

FROM restorepackages as publish

WORKDIR /src

COPY ["${INFRA_FOLDER}", "${INFRA_FOLDER}"]

WORKDIR "/src/${INFRA_FOLDER}"

RUN dotnet publish \
  --no-restore \
  -r $runtime \
  -c $buildConfig \
  --self-contained \
  -p:PublishTrimmed=true \
  -p:PublishReadyToRun=true \
  -p:PublishSingleFile=true \
  -p:PublishSelfContained=true \
  -o /app/publish

FROM base as final

COPY --from=publish --chown=$APP_UID:$APP_UID /app/publish /app

ENTRYPOINT [ "/app/Example.Infrastructure" ]