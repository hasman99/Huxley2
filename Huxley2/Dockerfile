FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build-env
WORKDIR /app
ARG TARGETARCH

# Copy csproj and restore as distinct layers
COPY *.csproj ./
RUN RID="linux-musl-${TARGETARCH}" \
  && if [ "${TARGETARCH}" = "amd64" ]; then RID="linux-musl-x64"; fi \
  && dotnet restore --runtime "${RID}"

# Copy everything else and build
COPY . ./
RUN RID="linux-musl-${TARGETARCH}" \
  && if [ "${TARGETARCH}" = "amd64" ]; then RID="linux-musl-x64"; fi \
  && dotnet publish -c Release -o out \
  --no-restore \
  --runtime "${RID}" \
  --self-contained true \
  /p:PublishSingleFile=true


# Build runtime image
FROM mcr.microsoft.com/dotnet/runtime-deps:8.0-alpine
WORKDIR /app
COPY --from=build-env /app/out .
ENTRYPOINT ["./Huxley2"]
