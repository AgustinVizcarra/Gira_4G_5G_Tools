#Defino mi imagen base sobre la que voy a trabajar
FROM ubuntu:jammy
#Defino una consola no interactiva para la generación de la imagen
ENV DEBIAN_FRONTEND=noninteractive
#Instalo la dependencia de los paquetes para la GUI, el simuladore de canal físico, UE e ENB
RUN apt-get update && \
    apt-get -y install build-essential \
    cmake \
    libfftw3-dev \
    libmbedtls-dev \
    libboost-program-options-dev \
    libtool \
    libconfig++-dev \
    libsctp-dev \
    gcc \
    g++ \
    make \
    net-tools \
    libzmq3-dev \
    libboost-system-dev \
    libboost-test-dev \
    libboost-thread-dev \
    libqwt-qt5-dev \
    qtbase5-dev \
    software-properties-common \
    libpython2-dev \
    pkg-config \
    swig \
    libi2c-dev \
    libpython3-dev \
    python3-numpy \
    python3-distutils \
    iputils-ping \
    git \
    iproute2\
    iperf3 \
    tcpdump \
    openssh-server
# Clono el repositorio con las herramientas de srsRAN para LTE
# Comienzo con la instancia de simulación de canal físico
RUN git clone https://github.com/zeromq/libzmq.git &&\
    cd libzmq &&\
    ./autogen.sh &&\
    ./configure &&\
    make -j `nproc` &&\
    make install && ldconfig
# De forma complementaria para czmq
RUN git clone https://github.com/zeromq/czmq.git &&\
    cd czmq &&\
    ./autogen.sh &&\
    ./configure &&\
    make -j `nproc` &&\
    make install && ldconfig
# Añadimos la GUI
RUN git clone https://github.com/srsLTE/srsGUI.git && \
    cd srsGUI && \
    mkdir build && \
    cd build && \
    cmake ../ && \
    make -j `nproc` && \
    make install && ldconfig
# Añadimos el SUITE de srsRAN para 4G (UE+eNB)
RUN git clone https://github.com/srsRAN/srsRAN_4G.git && \
    cd srsRAN_4G && \
    mkdir build && \
    cd build && \
    cmake ../ && \
    make -j `nproc` && \
    make install && ldconfig
# Instalamos systemctl
RUN apt-get -y install systemd
# Ejecutamos el modo vía consola bash
CMD [ "/bin/bash" ]