ARG DOTNET_VERSION="8.0-alpine"
FROM mcr.microsoft.com/dotnet/sdk:${DOTNET_VERSION} as base
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:${DOTNET_VERSION} as restorepackages

WORKDIR /src

ARG RUNTIME="linux-musl-x64"
ARG BUILD_CONFIG="Release"
ENV runtime=${RUNTIME}
ENV buildConfig=${BUILD_CONFIG}

ENV API_FOLDER="src/Api"
ENV API_ABS_FOLDER="src/Api.Abstractions"
ENV API_DTO_FOLDER="src/Api.Dto"
ENV API_SERVICES_FOLDER="src/Api.Services"
ENV DATA_DTO_FOLDER="src/Data.Dto"
ENV DATA_FOLDER="src/Data"

ENV API_TEST_FOLDER="test/Api.Test"
ENV SHARED_TEST_FOLDER="test/Shared.Test"
ENV DATA_SEED_TEST_FOLDER="test/Data.Seed.Test"

COPY ["*.props", ""]
COPY ["src/*.props", "src/"]
COPY ["test/*.props", "test/"]
COPY ["${DATA_DTO_FOLDER}/*.csproj","${DATA_DTO_FOLDER}/packages.lock.json*", "${DATA_DTO_FOLDER}/" ]
COPY ["${DATA_FOLDER}/*.csproj","${DATA_FOLDER}/packages.lock.json*", "${DATA_FOLDER}/" ]
COPY ["${API_DTO_FOLDER}/*.csproj","${API_DTO_FOLDER}/packages.lock.json*", "${API_DTO_FOLDER}/" ]
COPY ["${API_ABS_FOLDER}/*.csproj","${API_ABS_FOLDER}/packages.lock.json*", "${API_ABS_FOLDER}/" ]
COPY ["${API_SERVICES_FOLDER}/*.csproj","${API_SERVICES_FOLDER}/packages.lock.json*", "${API_SERVICES_FOLDER}/" ]
COPY ["${API_FOLDER}/*.csproj","${API_FOLDER}/packages.lock.json*", "${API_FOLDER}/" ]

COPY ["${DATA_SEED_TEST_FOLDER}/*.csproj","${DATA_SEED_TEST_FOLDER}/packages.lock.json*", "${DATA_SEED_TEST_FOLDER}/" ]
COPY ["${SHARED_TEST_FOLDER}/*.csproj","${SHARED_TEST_FOLDER}/packages.lock.json*", "${SHARED_TEST_FOLDER}/" ]
COPY ["${API_TEST_FOLDER}/*.csproj","${API_TEST_FOLDER}/packages.lock.json*", "${API_TEST_FOLDER}/" ]

WORKDIR "/src/${API_TEST_FOLDER}"

RUN dotnet restore \
  -r $runtime \
  -p:PublishReadyToRun=true

FROM restorepackages as publish

WORKDIR /src

COPY ["${API_DTO_FOLDER}","${API_DTO_FOLDER}" ]
COPY ["${DATA_DTO_FOLDER}","${DATA_DTO_FOLDER}" ]
COPY ["${DATA_FOLDER}","${DATA_FOLDER}" ]
COPY ["${API_ABS_FOLDER}","${API_ABS_FOLDER}" ]
COPY ["${API_SERVICES_FOLDER}","${API_SERVICES_FOLDER}" ]
COPY ["${API_FOLDER}","${API_FOLDER}" ]

COPY ["${DATA_SEED_TEST_FOLDER}", "${DATA_SEED_TEST_FOLDER}" ]
COPY ["${SHARED_TEST_FOLDER}", "${SHARED_TEST_FOLDER}" ]
COPY ["${API_TEST_FOLDER}", "${API_TEST_FOLDER}" ]

WORKDIR "/src/${API_TEST_FOLDER}"

RUN dotnet publish \
  --no-restore \
  -r $runtime \
  -c $buildConfig \
  -p:PublishTrimmed=true \
  -p:PublishReadyToRun=true \
  -o /app/publish

FROM base as final

COPY --from=publish --chown=$APP_UID:$APP_UID /app/publish /app

ENTRYPOINT [ "dotnet", "test", "/app/Example.Api.Test.dll" ]