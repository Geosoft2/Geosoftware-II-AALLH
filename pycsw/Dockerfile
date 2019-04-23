# =================================================================
#
# Authors: Ricardo Garcia Silva <ricardo.garcia.silva@gmail.com>
#
# Copyright (c) 2017 Ricardo Garcia Silva
#
# Permission is hereby granted, free of charge, to any person
# obtaining a copy of this software and associated documentation
# files (the "Software"), to deal in the Software without
# restriction, including without limitation the rights to use,
# copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the
# Software is furnished to do so, subject to the following
# conditions:
#
# The above copyright notice and this permission notice shall be
# included in all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
# EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
# OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
# NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
# HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
# WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
# FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
# OTHER DEALINGS IN THE SOFTWARE.
#
# =================================================================
FROM alpine:3.4

# There's bug in libxml2 v2.9.4 that prevents using an XMLParser with a schema
# file.
#
# https://bugzilla.gnome.org/show_bug.cgi?id=766834
#
# It seems to have been fixed upstream, but the fix has not been released into
# a new libxml2 version. As a workaround, we are sticking with the previous
# version, which works fine.
# This means that we need to use alpine's archives for version 3.1, which are
# the ones that contain the previous version of libxml2
#
# Also, for some unkwnon reason, alpine 3.1 version of libxml2 depends on
# python2. We'd rather use python3 for pycsw, so we install it too.
RUN echo 'http://dl-cdn.alpinelinux.org/alpine/v3.1/main' >> /etc/apk/repositories \
  && apk add --no-cache \
    build-base \
    ca-certificates \
    postgresql-dev \
    python3 \
    python3-dev \
    libpq \
    libxslt-dev \
    'libxml2<2.9.4' \
    'libxml2-dev<2.9.4' \
    wget \
  && apk add --no-cache \
    --repository http://dl-3.alpinelinux.org/alpine/edge/testing/ \
    --allow-untrusted \
    geos \
    geos-dev

RUN adduser -D -u 1000 pycsw

WORKDIR /tmp/pycsw

COPY \
  requirements-standalone.txt \
  requirements-pg.txt \
  requirements-dev.txt \
  requirements.txt \
  ./

RUN pip3 install --upgrade pip setuptools \
  && pip3 install --requirement requirements.txt \
  && pip3 install --requirement requirements-standalone.txt \
  && pip3 install --requirement requirements-pg.txt \
  && pip3 install gunicorn 

RUN apk add --no-cache \
    --repository http://dl-cdn.alpinelinux.org/alpine/edge/main/ \
    libcrypto1.1 \
    && apk add --no-cache \
    #--repository http://dl-cdn.alpinelinux.org/alpine/edge/testing/ \
    #gdal \
    #gdal-dev \
    geos \
    geos-dev \
    # list from https://git.alpinelinux.org/aports/tree/testing/gdal/APKBUILD?id=310ab1f4c74a1c3fd98d9baabf7e9c2c7b5f605b
    curl-dev \
	  giflib-dev \
	  jpeg-dev \
	  libjpeg-turbo-dev \
	  libpng-dev \
	  linux-headers \
	  postgresql-dev \
	  #python2-dev \
	  python3-dev \
	  sqlite-dev \
	  swig \
	  tiff-dev \
	  zlib-dev

# Problem: gdal aus alpine/edge/testing hat kein GEOS support
# Lösung: gdal zusätlich aus sourcen compilieren (dependencies sollten ja da sein), Version 2.4.0.0, Anleitung: https://trac.osgeo.org/gdal/wiki/BuildingOnUnix

ENV pkgname=gdal
ENV pkgver=2.4.0

WORKDIR /gdal

RUN wget http://download.osgeo.org/$pkgname/$pkgver/$pkgname-$pkgver.tar.xz \
  && unxz $pkgname-$pkgver.tar.xz \
  && tar xf $pkgname-$pkgver.tar

WORKDIR /gdal/$pkgname-$pkgver

# based on https://git.alpinelinux.org/aports/tree/testing/gdal/APKBUILD?id=310ab1f4c74a1c3fd98d9baabf7e9c2c7b5f605b
RUN	./configure --prefix=/usr \
  --with-curl=/usr/bin/curl-config \
  --with-geos=/usr/bin/geos-config \
  && make
# output includes
#NetCDF support:            no
# see also: https://bugs.alpinelinux.org/issues/8080
# https://bugs.alpinelinux.org/projects/alpine/search?utf8=%E2%9C%93&scope=subprojects&issues=1&q=gdal

#RUN swig/python \
#  && python3 setup.py build

RUN make install

# maybe this helps: https://github.com/nodejs/docker-node/issues/752
# maybe cleanup: RUN cd .. && rm -rf gdal-${GDAL_VERSION}*

RUN pip3 install pygdal

WORKDIR /tmp/pycsw

COPY pycsw pycsw/
COPY bin bin/
COPY setup.py .
COPY MANIFEST.in .
COPY VERSION.txt .
COPY README.rst .

RUN pip3 install .

COPY tests tests/
COPY docker docker/

ENV PYCSW_CONFIG=/etc/pycsw/aahll.cfg

RUN mkdir /etc/pycsw \
  && mv docker/aahll.cfg ${PYCSW_CONFIG} \
  && mkdir /var/lib/pycsw \
  && chown pycsw:pycsw /var/lib/pycsw \
  && cp docker/entrypoint.py /usr/local/bin/entrypoint.py \
  && chmod a+x /usr/local/bin/entrypoint.py \
  && cp -r tests /home/pycsw \
  && cp requirements.txt /home/pycsw \
  && cp requirements-standalone.txt /home/pycsw \
  && cp requirements-pg.txt /home/pycsw \
  && cp requirements-dev.txt /home/pycsw \
  && chown -R pycsw:pycsw /home/pycsw/* \
  && rm -rf *

WORKDIR /home/pycsw

USER pycsw

COPY db-data db-data/

EXPOSE 8000

ENTRYPOINT [\
  "python3", \
  "/usr/local/bin/entrypoint.py" \
]


