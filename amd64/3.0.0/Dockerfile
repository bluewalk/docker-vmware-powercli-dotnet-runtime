FROM mcr.microsoft.com/powershell:alpine-3.8

RUN apk add --no-cache \
    ca-certificates \
    # .NET Core dependencies
    krb5-libs \
    libgcc \
    libintl \
    libssl1.0 \
    libstdc++ \
    zlib \
    unzip \
    && rm -rf /tmp/* \ 
    && rm -fr /var/cache/apk/*

# Configure web servers to bind to port 80 when present
ENV ASPNETCORE_URLS=http://+:80 \
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true \
    # Set the invariant mode since icu_libs isn't included (see https://github.com/dotnet/announcements/issues/20)
    DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=true \
    # .NET Core version
    DOTNET_VERSION=3.0.0

RUN wget -O dotnet.tar.gz https://dotnetcli.blob.core.windows.net/dotnet/Runtime/$DOTNET_VERSION/dotnet-runtime-$DOTNET_VERSION-linux-musl-x64.tar.gz \
    && dotnet_sha512='a7382fbfabbfe859a66151760f199799d093a88c50a6f2fd97f31a7d6d688d9978526b9637c5e10b95a3155f76d5032cc7f054bb168915eeacf0dd759ada8b54' \
    && echo "$dotnet_sha512  dotnet.tar.gz" | sha512sum -c - \
    && mkdir -p /usr/share/dotnet \
    && tar -C /usr/share/dotnet -xzf dotnet.tar.gz \
    && ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet \
    && rm dotnet.tar.gz

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