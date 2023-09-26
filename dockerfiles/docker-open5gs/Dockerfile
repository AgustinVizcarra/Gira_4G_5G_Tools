# Definicion de la imagen base
FROM ubuntu:focal as builder

# Instalación no interactiva (x[Y or No]) 
ENV DEBIAN_FRONTEND=noninteractive

# Dependencias
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        python3-pip \
        python3-setuptools \
        python3-wheel \
        ninja-build \
        build-essential \
        flex \
        bison \
        git \
        cmake \
        libsctp-dev \
        libgnutls28-dev \
        libgcrypt-dev \
        libssl-dev \
        libidn11-dev \
        libmongoc-dev \
        libbson-dev \
        libyaml-dev \
        meson \
        mongodb \
        curl \
        gnupg \
        ca-certificates \
        libmicrohttpd-dev \
        libcurl4-gnutls-dev \
        libnghttp2-dev \
        libtins-dev \
        libidn11-dev \
        libtalloc-dev

#Instalación de node (para la compilación de la GUI)
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - && apt-get install -y nodejs

# Instalación de los binarios de Open5GS
RUN git clone --recursive https://github.com/open5gs/open5gs && cd open5gs && \
    git checkout 7f088730ed4bedc12f562e53de44697d5e1f5c6e && meson build --prefix=`pwd`/install && \
    ninja -C build && cd build && ninja install && \
    mkdir -p /open5gs/install/include

# Instalación de la GUI
RUN cd open5gs/webui && npm clean-install && npm run build

# Imagen Final
FROM ubuntu:focal

ENV DEBIAN_FRONTEND=noninteractive

#Dependencias finales para el funcionamiento y troubleshooting
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        sudo \
        curl \
        ca-certificates \
        libssl-dev \
        libyaml-dev \
        libmicrohttpd-dev \
        libmongoc-dev \
        libsctp-dev \
        libcurl4-gnutls-dev \
        libtins-dev \
        libidn11-dev \
        libtalloc-dev \
        netbase \
        ifupdown \
        net-tools \
        iputils-ping \
        python3-setuptools \
        python3-wheel \
        python3-pip \
        iptables \
        iproute2 \
        tcpdump \
        systemd \
        netbase \
        pkg-config \ 
        openssh-server \
        iperf3 && \
    apt-get autoremove -y && apt-get autoclean

RUN pip3 install click

RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - && apt-get install -y nodejs && \
    apt-get remove -y curl && apt-get autoremove -y && apt-get autoclean

RUN update-ca-certificates

# Movemos los directorios del constructor a la imagen final
COPY --from=builder /open5gs/install/bin /open5gs/install/bin
COPY --from=builder /open5gs/install/etc /open5gs/install/etc
COPY --from=builder /open5gs/install/include /open5gs/install/include
COPY --from=builder /open5gs/install/lib /open5gs/install/lib
COPY --from=builder /open5gs/webui /open5gs/webui

# Creamos el usuario SSH
RUN useradd -rm -d /home/ubuntu -s /bin/bash -g root -G sudo -u 1000 ubuntu
RUN echo 'ubuntu:ubuntu' | chpasswd && adduser ubuntu sudo 
RUN mkdir /var/run/sshd
EXPOSE 22

# Movemos los directorios de open5gs a /home/ubuntu

RUN mv /open5gs /home/ubuntu

# Terminamos con una instancia de bash que ejecute el container con el servicio SSH
CMD ["/usr/sbin/sshd", "-D"]