# Copyright 2017-2017 Amazon.com, Inc. or its affiliates. All Rights Reserved.
#
# Licensed under the Amazon Software License (the "License"). You may not use this file except in compliance with the License.
# A copy of the License is located at
#
#    http://aws.amazon.com/asl/
#
# or in the "license" file accompanying this file.
# This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, express or implied.
# See the License for the specific language governing permissions and limitations under the License.
#

FROM ubuntu:14.04.5

# Building git from source code:
#   Ubuntu's default git package is built with broken gnutls. Rebuild git with openssl.
##########################################################################
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       wget=1.15-* python=2.7.5-* python2.7-dev=2.7.6-* fakeroot=1.20-* ca-certificates \
       tar=1.27.1-* gzip=1.6-* zip=3.0-* autoconf=2.69-* automake=1:1.14.1-* \
       bzip2=1.0.6-* file=1:5.14-* g++=4:4.8.2-* gcc=4:4.8.2-* imagemagick=8:6.7.7.10-* \
       libbz2-dev=1.0.6-* libc6-dev=2.19-* libcurl4-openssl-dev=7.35.0-* libdb-dev=1:5.3.21~* \
       libevent-dev=2.0.21-stable-* libffi-dev=3.1~rc1+r3.0.13-* libgeoip-dev=1.6.0-* libglib2.0-dev=2.40.2-* \
       libjpeg-dev=8c-* libkrb5-dev=1.12+dfsg-* liblzma-dev=5.1.1alpha+20120614-* \
       libmagickcore-dev=8:6.7.7.10-* libmagickwand-dev=8:6.7.7.10-* libmysqlclient-dev=5.5.59-* \
       libncurses5-dev=5.9+20140118-* libpng12-dev=1.2.50-* libpq-dev=9.3.20-* libreadline-dev=6.3-* \
       libsqlite3-dev=3.8.2-* libssl-dev=1.0.1f-* libtool=2.4.2-* libwebp-dev=0.4.0-* \
       libxml2-dev=2.9.1+dfsg1-* libxslt1-dev=1.1.28-* libyaml-dev=0.1.4-* make=3.81-* \
       patch=2.7.1-* xz-utils=5.1.1alpha+20120614-* zlib1g-dev=1:1.2.8.dfsg-* unzip=6.0-* curl=7.35.0-* \
    && apt-get install -y -qq less=458-* groff=1.22.2-* \
    && apt-get -qy build-dep git=1:1.9.1 \
    && apt-get -qy install libcurl4-openssl-dev=7.35.0-* git-man=1:1.9.1-* liberror-perl=0.17-* \
    && mkdir -p /usr/src/git-openssl \
    && cd /usr/src/git-openssl \
    && apt-get source git=1:1.9.1 \
    && cd $(find -mindepth 1 -maxdepth 1 -type d -name "git-*") \
    && sed -i -- 's/libcurl4-gnutls-dev/libcurl4-openssl-dev/' ./debian/control \
    && sed -i -- '/TEST\s*=\s*test/d' ./debian/rules \
    && dpkg-buildpackage -rfakeroot -b \
    && find .. -type f -name "git_*ubuntu*.deb" -exec dpkg -i \{\} \; \
    && rm -rf /usr/src/git-openssl \
# Install dependencies by all python images equivalent to buildpack-deps:jessie
# on the public repos.
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

RUN wget "https://bootstrap.pypa.io/get-pip.py" -O /tmp/get-pip.py \
    && python /tmp/get-pip.py \
    && pip install awscli==1.11.157 \
    && rm -fr /var/lib/apt/lists/* /tmp/* /var/tmp/* 
 

ENV GOLANG_VERSION="1.6.3" \
    GOLANG_DOWNLOAD_SHA256="cdde5e08530c0579255d6153b08fdb3b8e47caabbe717bc7bcd7561275a87aeb" \
    GOPATH="/go" 

RUN mkdir -p "$GOPATH/src" "$GOPATH/bin" \
    && chmod -R 777 "$GOPATH" \
    && apt-get update && apt-get install -y --no-install-recommends \
        pkg-config=0.26-* \
    && apt-get clean \
    && wget "https://storage.googleapis.com/golang/go$GOLANG_VERSION.linux-amd64.tar.gz" -O /tmp/golang.tar.gz \
    && echo "$GOLANG_DOWNLOAD_SHA256 /tmp/golang.tar.gz" | sha256sum -c - \
    && tar -xzf /tmp/golang.tar.gz -C /usr/local \
    && rm -fr /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENV PATH="$GOPATH/bin:/usr/local/go/bin:$PATH"

WORKDIR $GOPATH
