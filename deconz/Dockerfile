# based on:
# https://github.com/marthoc/docker-deconz/blob/master/armhf/Dockerfile
# https://github.com/marthoc/docker-deconz/blob/master/armhf-hassio-addon/Dockerfile

ARG BUILD_FROM
FROM $BUILD_FROM

ENV LANG C.UTF-8

# Build environment variables
ENV DECONZ_VERSION=2.05.31 \
    WIRINGPI_VERSION="2.44+1"

# Build arugments
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION

# Install deCONZ dependencies (except for WiringPi)
RUN apt-get update && \
    apt-get install -y \
        curl \
        jq \
        kmod \
        libcap2-bin \
        libqt5core5a \
        libqt5gui5 \
        libqt5network5 \
        libqt5serialport5 \
        libqt5sql5 \
        libqt5websockets5 \
        libqt5widgets5 \
        sqlite3 \
        wget && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Add WiringPi, install WiringPi
ADD https://archive.raspberrypi.org/debian/pool/main/w/wiringpi/wiringpi_${WIRINGPI_VERSION}_armhf.deb /wiringpi.deb
RUN dpkg -i /wiringpi.deb && \
    rm -f /wiringpi.deb

# Add deCONZ, install deCONZ, make OTAU dir
ADD https://www.dresden-elektronik.de/rpi/deconz/beta/deconz-${DECONZ_VERSION}-qt5.deb /deconz.deb
RUN dpkg -i /deconz.deb && \
    rm -f /deconz.deb && \
    mkdir /root/otau && \
    chown root:root /usr/bin/deCONZ*

VOLUME [ "/root/.local/share/dresden-elektronik/deCONZ" ]

# Create directory for persistent Hass.io config data
# Workaround to persist ZigBee network data: change root's home dir to /data
RUN mkdir /data && \
    sed -i 's/\/root/\/data/' /etc/passwd && \
    chown root:root /usr/bin/deCONZ*
VOLUME [ "/data" ]

# Copy data for add-on
COPY run.sh /
RUN chmod a+x /run.sh

CMD [ "/run.sh" ]
