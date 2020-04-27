#
#  Licensed to the Apache Software Foundation (ASF) under one or more
#  contributor license agreements.  See the NOTICE file distributed with
#  this work for additional information regarding copyright ownership.
#  The ASF licenses this file to You under the Apache License, Version 2.0
#  (the "License"); you may not use this file except in compliance with
#  the License.  You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
#  Unless required by applicable law or agreed to in writing, software
#  distributed under the License is distributed on an "AS IS" BASIS,
#  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  See the License for the specific language governing permissions and
#  limitations under the License.
#

#
# build Zeek
#
FROM alpine as zeek
ARG ZEEK_VERSION=2.5.5

RUN apk add --no-cache \
  bash \
  binutils \
  bison \
  bsd-compat-headers \
  build-base \
  cmake \
  clang \
  flex \
  fts \
  fts-dev \
  git \
  libmaxminddb-dev \
  libpcap \
  libpcap-dev \
  linux-headers \
  python \
  python-dev \
  py-pip \
  openssl-dev \
  swig \
  zlib-dev \
  && rm -rf /var/cache/apk/*

RUN git clone --recursive https://github.com/zeek/zeek zeek \
  && cd zeek \
  && git checkout -b  v${ZEEK_VERSION} \
  && git submodule update --init --recursive \
  && ./configure --prefix=${PREFIX} \
  && make \
  && make install

#
# build Librdkafka
#
FROM alpine as librdkafka
ARG PREFIX=/usr/local
ARG RDK_VERSION=0.11.5

RUN apk add --no-cache \
  bash \
  build-base \
  curl \
  && rm -rf /var/cache/apk/*

RUN curl -L https://github.com/edenhill/librdkafka/archive/v${RDK_VERSION}.tar.gz | tar xvz \
  && cd librdkafka-${RDK_VERSION} \
  && ./configure --enable-sasl --prefix ${PREFIX} \
  && make \
  && make install

#
# build the Zeek plugin for Kafka
#

# TODO

#
# build final image
#
FROM centos:7
ARG PREFIX=/usr/local

# librdkafka
COPY --from=librdkafka ${PREFIX}/lib/     ${PREFIX}/lib/
COPY --from=librdkafka ${PREFIX}/include/ ${PREFIX}/include/

# TODO copy in the zeek bits
# see https://github.com/blacktop/docker-zeek/blob/master/3.1/Dockerfile

# TODO copy in the zeek plugin bits