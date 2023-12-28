ARG DOTNET_VERSION="8.0-alpine"
FROM mcr.microsoft.com/dotnet/sdk:${DOTNET_VERSION} as base
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:${DOTNET_VERSION} as restorepackages

WORKDIR /src

ARG RUNTIME="linux-musl-x64"
ARG BUILD_CONFIG="Release"
ENV runtime=${RUNTIME}
ENV buildConfig=${BUILD_CONFIG}

ENV INFRA_FOLDER="src/Infrastructure"

ENV INFRA_TEST_FOLDER="test/Frontend.Test"

COPY ["*.props", ""]
COPY ["src/*.props", "src/"]
COPY ["${INFRA_FOLDER}/*.csproj","${INFRA_FOLDER}/packages.lock.json*", "${INFRA_FOLDER}/" ]
COPY ["${INFRA_TEST_FOLDER}/*.csproj","${INFRA_TEST_FOLDER}/packages.lock.json*", "${INFRA_TEST_FOLDER}/" ]

WORKDIR "/src/${FRONTEND_TEST_FOLDER}"

RUN dotnet restore \
  -r $runtime \
  -p:PublishReadyToRun=true

FROM restorepackages as publish

WORKDIR /src

COPY ["${INFRA_FOLDER}", "${INFRA_FOLDER}" ]
COPY ["${INFRA_TEST_FOLDER}", "${INFRA_TEST_FOLDER}" ]

WORKDIR "/src/${INFRA_TEST_FOLDER}"

RUN dotnet publish \
  --no-restore \
  -r $runtime \
  -c $buildConfig \
  -p:PublishTrimmed=true \
  -p:PublishReadyToRun=true \
  -o /app/publish

FROM base as final

COPY --from=publish --chown=$APP_UID:$APP_UID /app/publish /app

ENTRYPOINT [ "dotnet", "test", "/app/Example.Frontend.Test.dll" ]