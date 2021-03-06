FROM microsoft/windowsservercore:ltsc2016 AS base

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# Install Git
RUN [Net.ServicePointManager]::SecurityProtocol = 'tls12, tls11, tls' ; \
    Invoke-WebRequest https://github.com/git-for-windows/git/releases/download/v2.19.1.windows.1/MinGit-2.19.1-64-bit.zip -OutFile git.zip; \
    Expand-Archive git.zip -DestinationPath $Env:ProgramFiles\Git ; \
    Remove-Item -Force git.zip

# Prepare TeamCity server distribution
ADD TeamCity-*.tar.gz /
RUN New-Item C:/TeamCity/webapps/ROOT/WEB-INF/DistributionType.txt -type file -force -value "docker-windows-sac2016" | Out-Null
COPY run-server.ps1 /TeamCity/run-server.ps1

FROM teamcity-base:latest-nanoserver-sac2016

COPY --from=base ["C:/Program Files/Git", "C:/Program Files/Git"]

ENV TEAMCITY_DIST="C:\TeamCity" \
    TEAMCITY_LOGS="C:\TeamCity\logs" \
    TEAMCITY_DATA_PATH="C:\ProgramData\JetBrains\TeamCity"

EXPOSE 8111

VOLUME $TEAMCITY_DATA_PATH \
       $TEAMCITY_LOGS

COPY --from=base $TEAMCITY_DIST $TEAMCITY_DIST

CMD powershell C:/TeamCity/run-server.ps1

RUN setx /M PATH "%PATH%;C:\Program Files\Git\cmd"