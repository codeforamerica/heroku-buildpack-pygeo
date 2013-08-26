Heroku buildpack: Py+Geo
========================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for Python apps, powered by [pip](http://www.pip-installer.org/).

It has been modified from the [Heroku original](https://github.com/heroku/heroku-buildpack-python) to add precompiled binaries for [GEOS](http://trac.osgeo.org/geos/), [PROJ.4](http://trac.osgeo.org/proj/), [GDAL](http://trac.osgeo.org/gdal/), and [Shapely](http://toblerity.org/shapely/).

Optionally, it will also fetch and convert a single zipped OGR datasource to `/app/datasource.shp` when provided with the environment variable `ZIPPED_DATA_URL`.
Note that [user-env-compile](https://devcenter.heroku.com/articles/labs-user-env-compile) from Heroku Labs must be enabled for this to work:

Usage
-----

Example usage:

    $ ls
    Procfile  requirements.txt  web.py

    $ heroku create --buildpack https://github.com/codeforamerica/heroku-buildpack-pygeo
    
The following two steps are optional, and will create `/app/datasource.shp` based on the remote file.

    $ heroku config:set ZIPPED_DATA_URL=http://forever.codeforamerica.org.s3.amazonaws.com/heroku-pygeo/data-sfgov-org-parcels.zip
    $ heroku labs:enable user-env-compile

Make it so.

    $ git push heroku master
    ...
    -----> Fetching custom git buildpack... done
    -----> Python app detected
    -----> No runtime.txt provided; assuming python-2.7.4.
    -----> Preparing Python runtime (python-2.7.4)
    -----> Installing Distribute (0.6.36)
    -----> Installing Pip (1.3.1)
    -----> Fetching and installing GEOS 3.3.8
           GEOS cached in /app/tmp/repo.git/.cache/vendor/geos
    -----> Fetching and installing Proj 4.8.0
           Proj cached in /app/tmp/repo.git/.cache/vendor/proj
    -----> Fetching and installing GDAL 1.10.0
           GDAL cached in /app/tmp/repo.git/.cache/vendor/gdal
    -----> Preparing datasource
           Downloading http://forever.codeforamerica.org.s3.amazonaws.com/heroku-pygeo/data-sfgov-org-parcels.zip to /app/tmp/repo.git/.cache/2aac292c2cbe849d129ccb4bbb7c1c3d.zip
           citylots.dbf --> /tmp/tmpiAkkBI/datasource.dbf
           citylots.prj --> /tmp/tmpiAkkBI/datasource.prj
           citylots.shp --> /tmp/tmpiAkkBI/datasource.shp
           citylots.shx --> /tmp/tmpiAkkBI/datasource.shx
           /tmp/tmpiAkkBI/datasource.shp --> /app/tmp/repo.git/.cache/datasource.shp
           (ogr2ogr might take a long time)
    -----> Installing dependencies using Pip (1.3.1)
           ...
           Successfully installed shapely
           Cleaning up...

You can also add it to upcoming builds of an existing application:

    $ heroku config:add BUILDPACK_URL=git://github.com/codeforamerica/heroku-buildpack-pygeo.git

The buildpack will detect your app as Python if it has the file `requirements.txt` in the root. 

It will use Pip to install your dependencies, vendoring a copy of the Python runtime into your slug. 

Specify a Runtime
-----------------

You can also provide arbitrary releases Python with a `runtime.txt` file.

    $ cat runtime.txt
    python-3.3.0
    
Runtime options include:

- python-2.7.4
- python-3.3.1
- pypy-1.9 (experimental)
