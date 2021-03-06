FROM google/cloud-sdk:alpine

RUN apk update
RUN apk add jq=1.6-r0
RUN pip3 install yq
RUN apk add openssl && \
    curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
RUN apk add perl-utils && \
    wget -O- https://k14s.io/install.sh | bash
RUN curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/linux/amd64/kubectl && \
    chmod +x ./kubectl && \
    mv ./kubectl /usr/local/bin/kubectl
RUN bosh_cli_version=$(curl --silent "https://api.github.com/repos/cloudfoundry/bosh-cli/releases/latest" | jq -r '.tag_name' | tr -d 'v') && \
    echo "Installing bosh_cli version ${bosh_cli_version}" && \
    curl -LO https://github.com/cloudfoundry/bosh-cli/releases/download/v${bosh_cli_version}/bosh-cli-${bosh_cli_version}-linux-amd64 && \
    chmod +x ./bosh-cli-${bosh_cli_version}-linux-amd64 && \
    mv ./bosh-cli-${bosh_cli_version}-linux-amd64 /usr/local/bin/bosh
RUN wget https://github.com/instrumenta/kubeval/releases/latest/download/kubeval-linux-amd64.tar.gz && \
    tar xf kubeval-linux-amd64.tar.gz && \
    chmod +x ./kubeval && \
    mv kubeval /usr/local/bin
RUN gcloud components install beta -q

# ginkgo
RUN export GO_TAR=go1.13.8.linux-amd64.tar.gz && \
    wget https://dl.google.com/go/${GO_TAR} && \
    tar -C /usr/local -xzf ${GO_TAR}

# https://github.com/golang/go/issues/28065
ENV CGO_ENABLED=0

ENV GOPATH /go
ENV PATH=$PATH:/go/bin:/usr/local/go/bin

RUN go get -u github.com/cloudfoundry/uptimer

# https://github.com/onsi/ginkgo/releases
RUN go get -u github.com/onsi/ginkgo/ginkgo

# https://github.com/cloudfoundry/cli/releases
WORKDIR /tmp
RUN \
  wget -O "cf-tar" "https://packages.cloudfoundry.org/stable?release=linux64-binary&version=6.50.0" && \
  tar xzvf cf-tar -C /usr/local/bin && \
  chmod +x /usr/local/bin/cf

RUN rm -rf /tmp/*
