Binary Notes
============

Build notes for each binary package used in `bin/compile`. These sequences created
[geos-3.3.8-heroku.tar.gz](http://forever.codeforamerica.org.s3.amazonaws.com/heroku-pygeo/geos-3.3.8-heroku.tar.gz),
[proj-4.8.0-heroku.tar.gz](http://forever.codeforamerica.org.s3.amazonaws.com/heroku-pygeo/proj-4.8.0-heroku.tar.gz),
and [gdal-1.10.0-heroku.tar.gz](http://forever.codeforamerica.org.s3.amazonaws.com/heroku-pygeo/gdal-1.10.0-heroku.tar.gz).

### GEOS

Download `geos-3.4.0.tar.bz2` from
[osgeo.org](http://trac.osgeo.org/geos/).
Unpack tarball and `cd` into the source directory.

    mkdir -pv /app/vendor/geos
    ./configure --prefix=/app/vendor/geos
    make && make install

    tar -C /app -czvf geos-3.3.8-heroku.tar.gz vendor/geos

### Proj

Download `proj-4.8.0.tar.gz` from
[osgeo.org](http://trac.osgeo.org/proj/).
Unpack tarball and `cd` into the source directory.

    mkdir -pv /app/vendor/proj
    ./configure --prefix=/app/vendor/proj
    make && make install

    tar -C /app -czvf proj-4.8.0-heroku.tar.gz vendor/proj

### GDAL

Download `gdal-1.10.0.tar.gz` from
[osgeo.org](http://trac.osgeo.org/gdal/wiki/DownloadSource).
Unpack tarball and `cd` into the source directory.

    mkdir -pv /app/vendor/gdal

    env LIBRARY_PATH=/app/vendor/proj/lib:/app/vendor/geos/lib:$LIBRARY_PATH \
        PATH=/app/vendor/geos/bin:$PATH \
        ./configure --prefix=/app/vendor/gdal --with-pcraster=no \
                    --with-jasper=no --with-grib=no --with-vfk=no \
                    --with-python --with-hide-internal-symbols

    make && make install

GDAL build puts the Python libraries in the wrong place, so we move them under `vendor/gdal`.

    mkdir -pv /app/vendor/gdal/lib/python2.7/site-packages

    cp -rf /app/.heroku/python/lib/python2.7/site-packages/GDAL-*.egg/*.* \
           /app/.heroku/python/lib/python2.7/site-packages/GDAL-*.egg/osgeo \
           /app/vendor/gdal/lib/python2.7/site-packages/

    tar -C /app -czvf gdal-1.10.0-heroku.tar.gz vendor/gdal
