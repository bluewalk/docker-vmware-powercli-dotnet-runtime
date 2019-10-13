FROM arm32v7/ubuntu:bionic

ENV PS_VERSION=6.2.0
ENV PS_PACKAGE=powershell-${PS_VERSION}-linux-arm32.tar.gz

RUN \
  apt-get update \
    && apt-get install -y --no-install-recommends \
      # Powershell dependencies
      ca-certificates \
      libunwind8 \
      libssl1.0 \
      libicu60 \
      wget \
      icu-devtools \
      \
      # .NET Core dependencies
      libc6 \
      libgcc1 \
      libgssapi-krb5-2 \
      libicu60 \
      libssl1.1 \
      libstdc++6 \
      zlib1g \
      curl \
  && wget https://github.com/PowerShell/PowerShell/releases/download/v${PS_VERSION}/${PS_PACKAGE} \
  && mkdir ~/powershell \
  && tar -xvf ./${PS_PACKAGE} -C ~/powershell \
  && ln -s /root/powershell/pwsh /usr/bin/pwsh \
  && apt-get purge wget -y \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

# Configure web servers to bind to port 80 when present
ENV ASPNETCORE_URLS=http://+:80 \
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true \
    # .NET Core version
    DOTNET_VERSION=3.0.0

RUN curl -SL --output dotnet.tar.gz https://dotnetcli.blob.core.windows.net/dotnet/Runtime/$DOTNET_VERSION/dotnet-runtime-$DOTNET_VERSION-linux-arm.tar.gz \
    && dotnet_sha512='b48854509ade35c3b74e462e45ad08ca5d08e536eaf52ae948b28769b5c7c29b29e287ce746ab040b43195dfde59cd734aacc88871644faf6a40490818bf350c' \
    && echo "$dotnet_sha512 dotnet.tar.gz" | sha512sum -c - \
    && mkdir -p /usr/share/dotnet \
    && tar -zxf dotnet.tar.gz -C /usr/share/dotnet \
    && rm dotnet.tar.gz \
    && ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet

WORKDIR /root

# Install VMware modules from PSGallery
RUN /usr/bin/pwsh -c Set-PSRepository -Name PSGallery -InstallationPolicy Trusted
RUN /usr/bin/pwsh -c Install-Module VMware.PowerCLI,PowerNSX,PowervRA

# Add the PowerCLI Example Scripts and Modules
RUN wget -O ./PowerCLI-Example-Scripts.zip https://github.com/vmware/PowerCLI-Example-Scripts/archive/master.zip && \
    unzip PowerCLI-Example-Scripts.zip && \
    rm -f PowerCLI-Example-Scripts.zip && \
    mv ./PowerCLI-Example-Scripts-master ./PowerCLI-Example-Scripts && \
    mv ./PowerCLI-Example-Scripts/Modules/* /opt/microsoft/powershell/6/Modules/