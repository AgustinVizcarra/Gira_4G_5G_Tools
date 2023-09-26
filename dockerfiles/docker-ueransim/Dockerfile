# Defino la imagen base que usará el constructor
FROM ubuntu:focal as builder
# admito una instalación de forma no interactiva
ENV DEBIAN_FRONTEND=noninteractive
# Versionamiento
LABEL Name=ueransim Version=0.0.2
#Instalación de dependencias 
RUN apt-get update && apt-get install -y wget \
    make \
    gcc \
    g++ \
    libsctp-dev \
    lksctp-tools \
    tcpdump \
    git \
    nano \
    iproute2 \
    iptables \
    net-tools \
    ifupdown \
    iputils-ping \
    libssl-dev
# con snap instalamos make
RUN mkdir ~/temp && \
    cd ~/temp && \
    wget https://cmake.org/files/v3.20/cmake-3.20.0.tar.gz && \
    tar -xzvf cmake-3.20.0.tar.gz && \
    cd cmake-3.20.0/ && \
    ./bootstrap && \
    make -j `nproc` && \
    make install && ldconfig && \
    cmake --version
# clonamos el repositorio de UERANSIM
RUN git clone https://github.com/aligungr/UERANSIM && \
    cd UERANSIM && \
    make -j `nproc`
# Construimos la imagen final
FROM ubuntu:focal 
# admito una instalación de forma no interactiva
ENV DEBIAN_FRONTEND=noninteractive
# Mantenemos las dependencias en nuestra imagen final
RUN apt-get update && apt-get install -y --no-install-recommends \
    libsctp-dev \
    lksctp-tools \
    tcpdump \
    nano \
    netbase \
    iproute2 \
    iptables \
    net-tools \
    ifupdown \
    iputils-ping \
    iperf3 \
    libssl-dev \
    systemctl \
    openssh-server && apt-get autoremove -y && apt-get autoclean
# Copiamos los archivos compilados del constructor a la imagen final
COPY --from=builder /UERANSIM/build /UERANSIM/build
COPY --from=builder /UERANSIM/config /UERANSIM/config
# Ejecutamos el contenedor en modo demonio
CMD ["/bin/bash"]
