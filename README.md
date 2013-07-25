geoip-heatmap-python
====================

Example python to perform GeoIP lookups for a list of IP addresses and then
generate heatmap using http://www.sethoscope.net/heatmap/


Overview
========

This example walks through downloading and installing the necessary tools to
take a list of IP addresses, perform GeoIP lookups to map those to
latitude/longitude coordinates, and generate a geographical heatmap using that
data.  

This procedure leverages several existing libraries and tools:

- GeoIP database and C/Python libraries to query the database

- Python libraries to work with OpenStreetMap data and images

- Python script that uses the OpenStreetMap/Imaging libraries to generate a
  heatmap to visualize a set latitude/longitude coordinates

Using the above, we wrote a quick Python script to perform GeoIP lookups for
all IP addresses listed in an input file that we can then feed into the heatmap
script to generate our map.

This document will run through a quick example using the tools we've already
built for the example or alternatively walk you through building all of the
prerequisite tools in your home directory.


Full install process for all prerequisites
==========================================

These instructions will walk you through the following:
1. Download latest GeoIP database
2. Download and install C/Python libraries to work with local GeoIP database
3. Download prerequisites for heatmap Python script
4. Generate list of recent IP addresses
5. Convert list of recent IP addresses into their latitude/longitude
   coordinates based on GeoIP database
6. Generate heatmap image using this location data

1. Grab a copy of GeoIP database to do local queries

```
$ wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz
$ gunzip GeoLiteCity.dat.gz
```

2. Download and install C/Python libraries to work with local GeoIP database

### Install GeoIP C library
```
$ cd $HOME/geoip-heatmap-python
$ wget https://www.maxmind.com/download/geoip/api/c/GeoIP-1.4.8.tar.gz
$ tar -zxf GeoIP-1.4.8.tar.gz
$ cd GeoIP-1.4.8
$ ./configure --prefix=$HOME/geoip-heatmap-python/build
$ make
$ make install
$ export PATH=$HOME/geoip-heatmap-python/build/bin
$ export LD_LIBRARY_PATH=$HOME/geoip-heatmap-python/build/lib:$LD_LIBRARY_PATH
```

### Install GeoIP Python library
```
$ cd $HOME/geoip-heatmap-python
$ wget https://www.maxmind.com/download/geoip/api/python/GeoIP-Python-1.2.7.tar.gz
$ tar -zxf GeoIP-Python-1.2.7.tar.gz
$ cd GeoIP-Python-1.2.7
```

#### Modify library_dirs and include_dirs variables in setup.py to include our GeoIP C library install directory
```
$ vim setup.py

$ python setup.py build
$ python setup.py install --prefix=$HOME/geoip-heatmap-python/build
$ export PYTHONPATH=$HOME/geoip-heatmap-python/build/lib64/python2.6/site-packages/:$PYTHONPATH
```

3. Download prerequisites for heatmap Python script

### osmviz - python library to work with OpenStreetMap
```
$ cd $HOME/geoip-heatmap-python
$ wget https://github.com/cbick/osmviz/archive/master.tar.gz
$ tar -zxf osmviz-master.tar.gz
$ cp -R osmviz-master/src/* $HOME/geoip-heatmap-python/build/lib64/python2.6/site-packages/
```

### PIL - python imaging library
```
$ cd $HOME/geoip-heatmap-python
$ wget http://effbot.org/downloads/Imaging-1.1.7.tar.gz
$ tar -zxf Imaging-1.1.7.tar.gz
$ cd Imaging-1.1.7/
$ python setup.py install --prefix=$HOME/geoip-heatmap-python/build
```


4. Generate list of recent IP addresses

```
$ cd $HOME/geoip-heatmap-python
$ last -i | awk '{print $3}' | grep ^[0-9] > recent-ips.txt
```

5. Convert list of recent IP addresses into their latitude/longitude
   coordinates based on GeoIP database

```
$ cd $HOME/geoip-heatmap-python
$ python geolite.py recent-ips.txt > recent-ips.points
```


6. Generate heatmap image using this location data

### Get the heatmap python script from http://www.sethoscope.net/heatmap/
```
$ cd $HOME/geoip-heatmap-python
$ wget http://www.sethoscope.net/heatmap/heatmap.py
$ python heatmap.py -p recent-ips.points -o recent-ips.png --width 800 --osm -d 0.5
```

