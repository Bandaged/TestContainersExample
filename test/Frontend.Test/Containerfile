ARG DOTNET_VERSION="8.0-alpine"
FROM mcr.microsoft.com/dotnet/sdk:${DOTNET_VERSION} as base
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:${DOTNET_VERSION} as restorepackages

WORKDIR /src

ARG RUNTIME="linux-musl-x64"
ARG BUILD_CONFIG="Release"
ENV runtime=${RUNTIME}
ENV buildConfig=${BUILD_CONFIG}

ENV API_DTO_FOLDER="src/Api.Dto"
ENV FRONTEND_FOLDER="src/Frontend"

ENV FRONTEND_TEST_FOLDER="test/Frontend.Test"
ENV SHARED_TEST_FOLDER="test/Shared.Test"
ENV DATA_SEED_TEST_FOLDER="test/Data.Seed.Test"

COPY ["*.props", ""]
COPY ["src/*.props", "src/"]
COPY ["test/*.props", "test/"]
COPY ["${API_DTO_FOLDER}/*.csproj","${API_DTO_FOLDER}/packages.lock.json*", "${API_DTO_FOLDER}/" ]
COPY ["${WORKER_FOLDER}/*.csproj","${WORKER_FOLDER}/packages.lock.json*", "${WORKER_FOLDER}/" ]
COPY ["${DATA_SEED_TEST_FOLDER}/*.csproj","${DATA_SEED_TEST_FOLDER}/packages.lock.json*", "${DATA_SEED_TEST_FOLDER}/" ]
COPY ["${SHARED_TEST_FOLDER}/*.csproj","${SHARED_TEST_FOLDER}/packages.lock.json*", "${SHARED_TEST_FOLDER}/" ]
COPY ["${FRONTEND_TEST_FOLDER}/*.csproj","${FRONTEND_TEST_FOLDER}/packages.lock.json*", "${FRONTEND_TEST_FOLDER}/" ]

WORKDIR "/src/${FRONTEND_TEST_FOLDER}"

RUN dotnet restore \
  -r $runtime \
  -p:PublishReadyToRun=true

FROM restorepackages as publish

WORKDIR /src

COPY ["${API_DTO_FOLDER}", "${API_DTO_FOLDER}"]
COPY ["${DATA_SEED_TEST_FOLDER}", "${DATA_SEED_TEST_FOLDER}" ]
COPY ["${SHARED_TEST_FOLDER}","${SHARED_TEST_FOLDER}" ]
COPY ["${FRONTEND_TEST_FOLDER}", "${FRONTEND_TEST_FOLDER}" ]

WORKDIR "/src/${FRONTEND_TEST_FOLDER}"

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