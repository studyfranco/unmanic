FROM debian:testing-slim
LABEL maintainer="Josh.5 <jsunnex@gmail.com>"


# Env variables
ENV \
    LIBVA_DRIVERS_PATH="/usr/lib/x86_64-linux-gnu/dri" \
    NVIDIA_DRIVER_CAPABILITIES="compute,video,utility" \
    NVIDIA_VISIBLE_DEVICES="all"


# Install the runtime dependencies
# TODO: Remove intel-opencl-icd and replace with suggested build by Jellyfin
#   https://jellyfin.org/docs/general/administration/hardware-acceleration.html
#   https://github.com/jellyfin/jellyfin/blob/master/Dockerfile

RUN set -x \
    && rm /etc/apt/sources.list.d/debian.sources \
    && echo "Acquire::Languages { \"fr\"; \"de\"; \"ru\"; \"en\";};" > /etc/apt/apt.conf.d/docker-no-languages \
    && echo "deb http://deb.debian.org/debian/ testing main contrib non-free non-free-firmware" > /etc/apt/sources.list \
    && apt update \
    && apt install -y ca-certificates apt-transport-https --no-install-recommends \
    && echo "" > /etc/apt/sources.list \
    && echo "Types: deb\nURIs: https://deb.debian.org/debian\nSuites: testing\nComponents: main contrib non-free non-free-firmware\nEnabled: yes\nSigned-By: /usr/share/keyrings/debian-archive-keyring.gpg" > /etc/apt/sources.list.d/debian.sources \
    && apt update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y locales \
    && echo "C.UTF-8 UTF-8\nde_DE.UTF-8 UTF-8\nen_GB.UTF-8 UTF-8\nen_US.UTF-8 UTF-8\nfr_FR.UTF-8 UTF-8\nru_RU.UTF-8 UTF-8" >> /etc/locale.gen \
    && echo "LANG=en_US.UTF-8\nLC_MESSAGES=en_US.UTF-8\nLANGUAGE=" > /etc/default/locale \
    && dpkg-reconfigure --frontend=noninteractive locales \
    && locale-gen \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y locales-all \
    && DEBIAN_FRONTEND=noninteractive apt purge -yy sudo \
    && apt dist-upgrade -y \
    && echo "\nTypes: deb\nURIs: https://deb.debian.org/debian\nSuites: unstable\nComponents: main contrib non-free non-free-firmware\nEnabled: yes\nSigned-By: /usr/share/keyrings/debian-archive-keyring.gpg" >> /etc/apt/sources.list.d/debian.sources \
    && echo "Package: *\nPin: release a=unstable\nPin-Priority: 490" > /etc/apt/preferences.d/list \
    && echo "Package: *\nPin: release a=stable\nPin-Priority: 480" >> /etc/apt/preferences.d/list \
    && echo "deb https://www.deb-multimedia.org testing main non-free" > /etc/apt/sources.list.d/multimedia.list \
    && apt-get update -oAcquire::AllowInsecureRepositories=true \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --allow-unauthenticated deb-multimedia-keyring --no-install-recommends \
    && rm /etc/apt/sources.list.d/multimedia.list \
    && echo "Types: deb\nURIs: https://www.deb-multimedia.org\nSuites: testing\nComponents: main non-free\nEnabled: yes\nSigned-By: /usr/share/keyrings/deb-multimedia-keyring.pgp" > /etc/apt/sources.list.d/multimedia.sources \
    && echo "\nTypes: deb\nURIs: https://www.deb-multimedia.org\nSuites: stable\nComponents: main non-free\nEnabled: yes\nSigned-By: /usr/share/keyrings/deb-multimedia-keyring.pgp" >> /etc/apt/sources.list.d/multimedia.sources \
    && echo "\nTypes: deb\nURIs: https://www.deb-multimedia.org\nSuites: unstable\nComponents: main non-free\nEnabled: yes\nSigned-By: /usr/share/keyrings/deb-multimedia-keyring.pgp" >> /etc/apt/sources.list.d/multimedia.sources \
    && apt autopurge -yy \
    && apt clean \
    && rm -rf /var/cache/* /var/lib/apt/lists/* /var/log/* /var/tmp/* /tmp/*

RUN set -x \
    && apt update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y nano less ffmpegthumbs tar wget curl pigz jq mkvtoolnix ffmpeg handbrake-cli va-driver-all mesa-utils mesa-va-drivers mesa-vulkan-drivers mesa-opencl-icd libgl1-mesa-dri libglx-mesa0 vulkan-tools rsync zip acl sed vainfo intel-media-va-driver-non-free firmware-intel-graphics firmware-intel-misc intel-opencl-icd hwinfo openssl sqlite3 jq nodejs libimage-exiftool-perl grc gcc python3 python3-dev python3-pip python3-setuptools unzip gosu --no-install-recommends --fix-missing \
    && apt dist-upgrade -y \
    && apt autopurge -yy \
    && apt clean autoclean -y \
    && rm -rf /var/cache/* /var/lib/apt/lists/* /var/log/* /var/tmp/* /tmp/*

RUN python3 -m pip install unmanic \
    && mkdir -p /config \
    && mkdir -p /library \
    && adduser --system --no-create-home --gecos "" --home /config unmanic

# Unmanic runs on port 8888
EXPOSE 8888/tcp

COPY --chmod=0755 /docker/files/run.sh /run.sh
COPY --chmod=0755 /docker/files/entrypoint.sh /entrypoint.sh
CMD ["/entrypoint.sh"]