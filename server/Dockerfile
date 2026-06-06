# builder
FROM ubuntu:22.04 as builder

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
    git=1:2.34.1-1ubuntu1.11 \
    clang=1:14-1 \
    cmake=3.22.1-1ubuntu1.2 \
    make=4.3-4.1 \
    gcc=4:11.2.0-2ubuntu1 \
    g++=4:11.2.0-2ubuntu1 \
    libmysqlclient-dev=8.0.35-1ubuntu1 \
    libssl-dev=3.0.2-0ubuntu1.15 \
    libbz2-dev=1.0.8-5ubuntu1 \
    libreadline-dev=8.1-1ubuntu1 \
    libncurses-dev=6.3-2ubuntu1 \
    libboost-all-dev=1.74.0.3ubuntu7 \
    mariadb-client=1:10.6.12-1ubuntu0.1 \
    p7zip-full=16.02+dfsg-8

WORKDIR /source

RUN git clone -b 3.3.5 --depth 1 https://github.com/TrinityCore/TrinityCore.git .

WORKDIR /source/build
RUN cmake ../ -DCMAKE_INSTALL_PREFIX=/opt/server \
    -DTOOLS=0 \
    -DWITH_WARNINGS=0

RUN make -j "$(nproc)" && make install


FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
    libmysqlclient21=8.0.35-1ubuntu1 \
    libssl3=3.0.2-0ubuntu1.15 \
    libboost-system1.74.0=1.74.0-13ubuntu1 \
    libboost-filesystem1.74.0=1.74.0-13ubuntu1 \
    libboost-program-options1.74.0=1.74.0-13ubuntu1 \
    libboost-iostreams1.74.0=1.74.0-13ubuntu1 \
    libboost-regex1.74.0=1.74.0-13ubuntu1 \
    libboost-locale1.74.0=1.74.0-13ubuntu1 \
    libboost-thread1.74.0=1.74.0-13ubuntu1 \
    libboost-chrono1.74.0=1.74.0-13ubuntu1 \
    libboost-date-time1.74.0=1.74.0-13ubuntu1 \
    libreadline8=8.1-1ubuntu1 \
    netcat=1.10-46.1ubuntu1 \
    mariadb-client=1:10.6.12-1ubuntu0.1 \
    p7zip-full=16.02+dfsg-8 \
    dos2unix=7.4.0-1 \
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