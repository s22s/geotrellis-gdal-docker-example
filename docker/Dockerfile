FROM ubuntu:cosmic

# UTF-8 all the things
RUN \
    apt-get clean -y && apt-get update && \
    apt-get install -y apt-utils locales && \
    locale-gen en_US.UTF-8 && \
    apt-get clean all

# ENVs
ENV LANG en_US.UTF-8
ENV LC_ALL en_US.UTF-8
ENV ROOTDIR /usr/local/
# OPENJPEG versions prior to 2.3.0 have problems processing large jp2 files
# https://lists.osgeo.org/pipermail/gdal-dev/2017-October/047397.html
ENV OPENJPEG_VERSION 2.3.0
ENV GDAL_VERSION 2.3.3
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64/
ENV SCALA_VERSION 2.12.8
ENV SBT_VERSION 1.2.8

WORKDIR $ROOTDIR/

RUN \
    apt-get update && \
    apt-get install -y \
        software-properties-common \
        python3-software-properties \
        gcc \
        g++ \
        curl \
        build-essential \
        python-dev \
        python-numpy \
        python3-dev \
        python3-numpy \
        python3-pip \
        libcurl4-gnutls-dev \
        libproj-dev \
        libgeos-dev \
        libhdf4-alt-dev \
        libhdf5-serial-dev \
        bash-completion \
        cmake \
        imagemagick \
        libpng-dev \
        wget \
        swig \
        ant \
        openjdk-8-jdk-headless \
    && rm -rf /var/lib/apt/lists/* && apt-get autoremove -y && apt-get clean all

# Install Pip and AWS CLI
RUN pip3 install --upgrade pip awscli

# Compile and install OpenJPEG for GDAL
RUN \
    cd $ROOTDIR/src && \
    wget https://github.com/uclouvain/openjpeg/archive/v${OPENJPEG_VERSION}.tar.gz && \
    tar -xvf v${OPENJPEG_VERSION}.tar.gz && \
    cd openjpeg-${OPENJPEG_VERSION}/ && \
    mkdir build && \
    cd build && \
    cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$ROOTDIR && \
    make -j && \
    make install && \
    rm -Rf $ROOTDIR/src/openjpeg* $ROOTDIR/src/v${OPENJPEG_VERSION}.tar.gz

# Compile and install GDAL
# HDF4 - included, used by MODIS
# JPEG2000 - openjpeg, used by Sentinel-2
# MRF - included, used by NAIP
# GeoTIFF - included, use internal version
RUN \
    cd $ROOTDIR/src && \
    wget http://download.osgeo.org/gdal/${GDAL_VERSION}/gdal-${GDAL_VERSION}.tar.gz && \
    tar -xvf gdal-${GDAL_VERSION}.tar.gz && \
    cd gdal-${GDAL_VERSION} && \
    ./configure \
        --with-python \
        --with-curl \
        --with-openjpeg \
        --with-hdf4 \
        --with-hdf5 \
        --with-geos \
        --with-geotiff=internal \
        --with-hide-internal-symbols \
        --with-libtiff=internal \
        --with-libz=internal \
        --with-threads \
        --with-mrf \
        --without-jp2mrsid \
        --without-netcdf \
        --without-ecw \
        --with-java=$JAVA_HOME \
    && \
    make -j 8 && \
    make install && \
    cd swig/java && \
    make install && \
    ldconfig && \
    rm -Rf $ROOTDIR/src/gdal*
   # cd $ROOTDIR/src/gdal-${GDAL_VERSION}/swig/python && \
   # python3 setup.py build && \
   # python3 setup.py install && \

# Externally accessible data is by default put in /data
WORKDIR /data
VOLUME ["/data"]

# Output version and capabilities by default.
CMD gdalinfo --version && gdalinfo --formats && java -version && javac -version && echo sbt sbtVersion: `sbt sbtVersion` && aws --version
