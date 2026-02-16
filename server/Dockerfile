# builder
FROM ubuntu:22.04 as builder

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    git clang cmake make gcc g++ libmysqlclient-dev \
    libssl-dev libbz2-dev libreadline-dev libncurses-dev \
    libboost-all-dev mariadb-client p7zip-full

WORKDIR /source

RUN git clone -b 3.3.5 --depth 1 https://github.com/TrinityCore/TrinityCore.git .

WORKDIR /source/build
RUN cmake ../ -DCMAKE_INSTALL_PREFIX=/opt/server \
    -DTOOLS=0 \
    -DWITH_WARNINGS=0

RUN make -j $(nproc) && make install


FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    libmysqlclient21 libssl3 libboost-system1.74.0 \
    libboost-filesystem1.74.0 libboost-program-options1.74.0 \
    libboost-iostreams1.74.0 libboost-regex1.74.0 \
    libboost-locale1.74.0 libboost-thread1.74.0 \
    libboost-chrono1.74.0 libboost-date-time1.74.0 \
    libreadline8 netcat mariadb-client p7zip-full dos2unix \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /opt/server /opt/server
COPY --from=builder /source/sql /opt/server/sql

COPY wait-for-it.sh /opt/server/bin/wait-for-it.sh
RUN dos2unix /opt/server/bin/wait-for-it.sh && chmod +x /opt/server/bin/wait-for-it.sh

WORKDIR /opt/server/bin

# WorldServer
EXPOSE 8085
# AuthServer
EXPOSE 3724

CMD ["./worldserver"]