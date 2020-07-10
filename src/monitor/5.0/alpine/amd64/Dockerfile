ARG ASPNET_REPO=mcr.microsoft.com/dotnet/core/aspnet
ARG SDK_REPO=mcr.microsoft.com/dotnet/core/sdk

# Installer image
FROM $SDK_REPO:3.1-alpine3.12 AS installer

# Install .NET Monitor
ENV DOTNET_MONITOR_VERSION=5.0.0-preview.2.20359.4
RUN wget -O dotnet-monitor.$DOTNET_MONITOR_VERSION.nupkg https://dotnetcli.azureedge.net/dotnet/diagnostics/monitor5.0/dotnet-monitor.$DOTNET_MONITOR_VERSION.nupkg \
    && dotnetmonitor_sha512='899ac17f3332e7b8bcb1166f2e428d9a7a7616e804a9ad0fc30ae51f5250409a43576c6a01b27b4ad9ae22b98137e749a3d32ad8c905277498252050b5a83991' \
    && echo "$dotnetmonitor_sha512  dotnet-monitor.$DOTNET_MONITOR_VERSION.nupkg" | sha512sum -c - \
    && dotnet tool install dotnet-monitor --tool-path /app --add-source / --version $DOTNET_MONITOR_VERSION --no-cache \
    # To reduce image size, remove the copy of the nupkg under the tools.
    && find /app -print | grep -i '.*[.]nupkg$' | xargs rm \
    && rm dotnet-monitor.$DOTNET_MONITOR_VERSION.nupkg


# Monitor image
FROM $ASPNET_REPO:3.1-alpine3.12

WORKDIR /app
COPY --from=installer /app .

ENV \
    # Disable debugger and profiler diagnostics to avoid diagnosing self.
    COMPlus_EnableDiagnostics=0 \
    # Add dotnet-monitor path to front of PATH for easier, prioritized execution
    PATH="/app:${PATH}"

ENTRYPOINT ["dotnet-monitor", "collect"]