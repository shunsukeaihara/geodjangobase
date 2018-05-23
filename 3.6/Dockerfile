FROM python:3.6.5-alpine3.6

ENV GEOS_VERSION 3.5.1
ENV PROJ_VERSION 4.9.3
ENV PROJ_DATUMGRID_VERSION 1.6
ENV GDAL_VERSION 2.2.4

RUN apk add --no-cache --virtual .geo-dev gcc g++ make linux-headers  \
    # install geos
    && cd /tmp \
    && wget http://download.osgeo.org/geos/geos-${GEOS_VERSION}.tar.bz2 \
    && tar xjf geos-${GEOS_VERSION}.tar.bz2 \
    && cd geos-${GEOS_VERSION} \
    && ./configure --enable-silent-rules CFLAGS="-D__sun -D__GNUC__"  CXXFLAGS="-D__GNUC___ -D__sun" \
    && make -s \
    && make -s install \
    # install proj
    && cd /tmp \
    && wget http://download.osgeo.org/proj/proj-${PROJ_VERSION}.tar.gz \
    && wget http://download.osgeo.org/proj/proj-datumgrid-${PROJ_DATUMGRID_VERSION}.zip \
    && tar xzf proj-${PROJ_VERSION}.tar.gz \
    && cd proj-${PROJ_VERSION}/nad \
    && unzip ../../proj-datumgrid-${PROJ_DATUMGRID_VERSION}.zip \
    && cd .. \
    && ./configure --enable-silent-rules \
    && make -s \
    && make -s install \
    # install gdal
    && cd /tmp \
    && wget http://download.osgeo.org/gdal/${GDAL_VERSION}/gdal-${GDAL_VERSION}.tar.gz \
    && tar xzf gdal-${GDAL_VERSION}.tar.gz \
    && cd gdal-${GDAL_VERSION} \
    && ./configure --enable-silent-rules --with-static-proj4=/usr/local/lib \
    && make -s \
    && make -s install \
    && runDeps="$( \
            scanelf --needed --nobanner --recursive /usr/local/ \
                    | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
                    | sort -u \
                    | xargs -r apk info --installed \
                    | sort -u \
    )" \
    && apk add --virtual .geo-rundeps $runDeps \
    && apk del --purge .geo-dev \
    && rm -rf /tmp/* \
    && rm -rf /root/.cache

CMD /bin/sh
