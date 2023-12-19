ARG DEBIAN_VERSION="latest"
ARG K3S_INSTALL_VERSION="v1.26.5+k3s1"
ARG KUBECTL_INSTALL_VERSION="v1.29"
ARG MICROSOFT_DEBIAN_VERSION="12"
FROM debian:${DEBIAN_VERSION}

RUN apt-get update -y
RUN apt-get install -y apt-transport-https ca-certificates curl wget

# setup dotnet
RUN wget "https://packages.microsoft.com/config/debian/${MICROSOFT_DEBIAN_VERSION}/packages-microsoft-prod.deb" -O packages-microsoft-prod.deb
RUN dpkg -i packages-microsoft-prod.deb
RUN rm packages-microsoft-prod.deb

RUN apt-get update -y
RUN apt-get install -y dotnet-sdk-6.0 dotnet-sdk-7.0 dotnet-sdk-8.0

# setup docker
RUN install -m 0755 -d /etc/apt/keyrings
RUN curl -fsSL https://download.docker.com/linux/debian/gpg \
  | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
RUN chmod a+r /etc/apt/keyrings/docker.gpg

RUN echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null

RUN apt-get update -y
RUN apt-get install -y docker docker-compose

# setup kubectl
RUN curl -fsSL "https://pkgs.k8s.io/core:/stable:/${KUBECTL_INSTALL_VERSION}/deb/Release.key" \
  | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

RUN apt-get update -y
RUN apt-get install -y kubectl

# setup helm
RUN curl https://baltocdn.com/helm/signing.asc \
  | gpg --dearmor | tee /usr/share/keyrings/helm.gpg > /dev/
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" \
  | tee /etc/apt/sources.list.d/helm-stable-debian.list

RUN apt-get update -y
RUN apt-get install -y helm
RUN helm plugin install https://github.com/helm-unittest/helm-unittest.git

# setup k3s
RUN curl -Lo /usr/local/bin/k3s "https://github.com/k3s-io/k3s/releases/download/${K3S_INSTALL_VERSION}/k3s"
RUN chmod a+x /usr/local/bin/k3s
