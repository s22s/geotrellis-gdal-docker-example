# Geotrellis GDAL Example Project

The purpose of this project is to have a concise example of creating a Docker image with
GDAL compiled with Java JNI bindings, and then running a Geotrellis GDAL read from that image.

## TL;DR

    cd docker
    docker build -t gdal-java:latest . 
    cd ..
    sbt dpl
    aws s3 cp s3://landsat-pds/c1/L8/200/110/LC08_L1GT_200110_20170907_20170907_01_RT/LC08_L1GT_200110_20170907_20170907_01_RT_B3.TIF .
    aws s3 cp s3://sentinel-s2-l2a/tiles/35/R/NP/2019/1/7/0/R10m/B03.jp2 . --request-payer
    aws s3 cp s3://modis-pds/MCD43A4.006/21/11/2017006/MCD43A4.A2017006.h21v11.006.2017018074804_B01.TIF .
    docker run -it -v $(pwd):/data geotrellis-gdal-example
    
## Base Docker Image (docker/Dockerfile)

This Dockerfile creates and image with the following:
* Ubuntu 18.10 (Cosmic Cuttlefish)
* GDAL, compiled with HDF4, HDF5, JPEG2000 (OpenJPEG), and Java support
* OpenJDK JDK 8
* Python 3
* AWS CLI

### Provenance
* GDAL derived from [s22s/geo-swak](https://github.com/s22s/geo-swak), which itself is based 
  mostly on [geographica/gdal2](https://github.com/GeographicaGS/Docker-GDAL2), and to a lesser extent 
  [geodata/gdal](https://github.com/geo-data/gdal-docker)
* Python install based on [https://askubuntu.com/questions/865554/how-do-i-install-python-3-6-using-apt-get]

