ARG DOTNET_VERSION="8.0-alpine"
FROM mcr.microsoft.com/dotnet/runtime-deps:${DOTNET_VERSION} as base
WORKDIR /app
USER $APP_UID

FROM mcr.microsoft.com/dotnet/sdk:${DOTNET_VERSION} as restorepackages

WORKDIR /src

ARG RUNTIME="linux-musl-x64"
ARG BUILD_CONFIG="Release"
ENV runtime=${RUNTIME}
ENV buildConfig=${BUILD_CONFIG}

ENV API_DTO_FOLDER="src/Api.Dto"
ENV DATA_DTO_FOLDER="src/Data.Dto"
ENV DATA_FOLDER="src/Data"
ENV WORKER_FOLDER="src/Worker"
ENV WORKER_ABS_FOLDER="src/Worker.Abstractions"
ENV WORKER_DTO_FOLDER="src/Worker.Dto"
ENV WORKER_SERVICES_FOLDER="src/Worker.Services"

COPY ["*.props", ""]
COPY ["src/*.props", "src/"]
COPY ["${API_DTO_FOLDER}/*.csproj","${API_DTO_FOLDER}/packages.lock.json*", "${API_DTO_FOLDER}/" ]
COPY ["${DATA_DTO_FOLDER}/*.csproj","${DATA_DTO_FOLDER}/packages.lock.json*", "${DATA_DTO_FOLDER}/" ]
COPY ["${WORKER_DTO_FOLDER}/*.csproj","${WORKER_DTO_FOLDER}/packages.lock.json*", "${WORKER_DTO_FOLDER}/" ]
COPY ["${DATA_FOLDER}/*.csproj","${DATA_FOLDER}/packages.lock.json*", "${DATA_FOLDER}/" ]
COPY ["${WORKER_ABS_FOLDER}/*.csproj","${WORKER_ABS_FOLDER}/packages.lock.json*", "${WORKER_ABS_FOLDER}/" ]
COPY ["${WORKER_SERVICES_FOLDER}/*.csproj","${WORKER_SERVICES_FOLDER}/packages.lock.json*", "${WORKER_SERVICES_FOLDER}/" ]
COPY ["${WORKER_FOLDER}/*.csproj","${WORKER_FOLDER}/packages.lock.json*", "${WORKER_FOLDER}/" ]

WORKDIR "/src/${WORKER_FOLDER}"

RUN dotnet restore \
  -r $runtime \
  -p:PublishReadyToRun=true \
  -p:PublishSelfContained=true \
  -p:PublishSingleFile=true

FROM restorepackages as publish

WORKDIR /src

COPY ["${API_DTO_FOLDER}", "${API_DTO_FOLDER}"]
COPY ["${DATA_DTO_FOLDER}", "${DATA_DTO_FOLDER}"]
COPY ["${WORKER_DTO_FOLDER}", "${WORKER_DTO_FOLDER}"]
COPY ["${WORKER_ABS_FOLDER}", "${WORKER_ABS_FOLDER}"]
COPY ["${WORKER_SERVICES_FOLDER}", "${WORKER_SERVICES_FOLDER}"]
COPY ["${WORKER_FOLDER}", "${WORKER_FOLDER}"]

WORKDIR "/src/${WORKER_FOLDER}"

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

ENTRYPOINT [ "/app/Example.Worker" ]