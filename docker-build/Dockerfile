FROM debian:jessie-slim

LABEL maintainer="lordkator@swgemu.com"

ENV ZC_VERSION='1.0.0' \
    ZC_BUILDER='lordkator@swgemu.com' \
    CORE3_REPO_URL=https://github.com/TheAnswer/Core3.git \
    CORE3_BRANCH=unstable \
    ENGINE_REPO_URL=https://github.com/TheAnswer/PublicEngine.git \
    ENGINE_BRANCH=master \
    USER=swgemu \
    HOME=/home/swgemu \
    SHELL=/bin/bash \
    TZ=Etc/UTC

# EXPOSE 44480 # HTTP EmuYoda - Dangerous!
#      HTTPS EmuYoda | StatusPort | LoginPort | PingPort | GalaxyPort
EXPOSE 44443           44455        44453       44462      44463

# You can control where syslog messages from ZC go with this setting:
# ENV LOGGER_ARGS "--server mysyslog --port blah"

## Add extra packages to keep for running here...
# ENV EXTRA_PACKAGES="iftop"

## You should pass these on run in environment or via NAME_FILE
# DBHOST
# DBPORT
# DBNAME
# DBUSER
# DBPASS
# DBSECRET

ENV DBHOST="${DBHOST:-127.0.0.1}" \
    DBPORT="${DBPORT:-3360}" \
    DBNAME="${DBNAME:-swgemu}" \
    DBUSER="${DBUSER:-swgemu}" \
    DBPASS="${DBPASS:-12345678}" \
    DBSECRET="${DBSECRET:-9EiQc2zxzvFNLsuNMbTQ}"

WORKDIR ${HOME}

RUN set -xe; \
# Create user
    useradd -m -U swgemu -G adm,sudo -s /bin/bash; \
    mkdir -p /core3-data; \
    echo 'APT::Install-Recommends "0";\nAPT::Install-Suggests "0";' > /etc/apt/apt.conf.d/01norecommends; \
    echo "deb http://ftp.debian.org/debian jessie-backports main" >> /etc/apt/sources.list; \
    apt-get update; \
    apt-get upgrade -y; \
    apt-get install -y moreutils git ca-certificates; \
    apt-get -t jessie-backports install gosu; \
# Avoid java install failure, see https://github.com/kubernetes/kubernetes/issues/1806
    for i in 1 2 3 4 5 6 7 8; do mkdir -p /usr/share/man/man$i; done;
# Run ZonamaContainer build

RUN set -xe; \
    git clone --progress -b master https://github.com/lordkator/ZonamaContainer.git; \
    ZonamaContainer/container/scripts/build; \
    gosu swgemu ZonamaContainer/container/scripts/setup-server;

VOLUME "/core3-data"

ENTRYPOINT ["/home/swgemu/ZonamaContainer/container/scripts/docker-entrypoint.sh"]

CMD ["/usr/bin/supervisord"]
