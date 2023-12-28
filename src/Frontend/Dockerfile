ARG DOTNET_VERSION="8.0-alpine"
FROM mcr.microsoft.com/dotnet/runtime-deps:${DOTNET_VERSION} as base
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:${DOTNET_VERSION} as restorepackages
WORKDIR /src

ARG RUNTIME="linux-musl-x64"
ARG BUILD_CONFIG="Release"
ENV runtime=${RUNTIME}
ENV buildConfig=${BUILD_CONFIG}

ENV FRONTEND_FOLDER="src/Frontend"
ENV API_DTO_FOLDER="src/Api.Dto"

COPY ["*.props", ""]
COPY ["src/*.props", "src/"]
COPY ["${API_DTO_FOLDER}/*.csproj","${API_DTO_FOLDER}/packages.lock.json*", "${API_DTO_FOLDER}/" ]
COPY ["${FRONTEND_FOLDER}/*.csproj","${FRONTEND_FOLDER}/packages.lock.json*", "${FRONTEND_FOLDER}/" ]

WORKDIR "/src/${FRONTEND_FOLDER}"

RUN dotnet restore \
  -r $runtime \
  -p:PublishReadyToRun=true \
  -p:PublishSelfContained=true \
  -p:PublishSingleFile=true

FROM restorepackages as publish

WORKDIR /src

COPY ["${API_DTO_FOLDER}","${API_DTO_FOLDER}" ]
COPY ["${FRONTEND_FOLDER}","${FRONTEND_FOLDER}" ]

WORKDIR "/src/${FRONTEND_FOLDER}"

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

ENTRYPOINT [ "/app/Example.Frontend" ]