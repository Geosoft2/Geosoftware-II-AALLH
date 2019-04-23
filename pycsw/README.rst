A²HL²-pycsw README 
===========================================

Information about pycsw
========================

.. image:: https://travis-ci.org/geopython/pycsw.svg?branch=master
    :target: https://travis-ci.org/geopython/pycsw

pycsw is an OGC CSW server implementation written in Python.

pycsw fully implements the OpenGIS Catalogue Service Implementation 
Specification (Catalogue Service for the Web). Initial development started in 
2010 (more formally announced in 2011). The project is certified OGC 
Compliant, and is an OGC Reference Implementation.  Since 2015, pycsw is an 
official OSGeo Project.

pycsw allows for the publishing and discovery of geospatial metadata via 
numerous APIs (CSW 2/CSW 3, OpenSearch, OAI-PMH, SRU). Existing repositories 
of geospatial metadata can also be exposed, providing a standards-based 
metadata and catalogue component of spatial data infrastructures.

pycsw is Open Source, released under an MIT license, and runs on all major 
platforms (Windows, Linux, Mac OS X).

Please read the docs at http://pycsw.org/docs for more information.

Installation guide for A²HL²-pycsw
==================================

Install from Code - Setting up a development environment with docker
========================================================================

- install docker: [https://docs.docker.com/install/](https://docs.docker.com/install/)
- clone our repository to your computer
- make sure, docker is running
- open Windows PowerShell (Windows with Docker), DockerToolbox or the Terminal (MacOS) 
- navigate into the pycsw Folder
- then add the following:

```
docker build -t aahll-pycsw .
```

This could take an hour or more. 

Important for the following steps: When you get an input/output error anytime while you install pycsw, simply restart Docker and try again.

Installation on Windows with Docker (not Docker Toolbox)
----------------------------------------------------------

- go in the folder of the cloned repository where the bootCode.bat file is and double click on this file

- that is everything you have to do 
	- now go to localhost:8000 for pycsw or to localhost:5000 for our map

Installation on Windows with Docker Toolbox
------------------------------------------------

- open DockerToolbox 
- navigate in the pycsw folder in the cloned repository
- add the following (just completely copy and paste):

```
docker run --name pycsw-dev --volume ${PWD}/pycsw:/usr/lib/python3.5/site-packages/pycsw --volume ${PWD}/docs:/home/pycsw/docs --volume ${PWD}/VERSION.txt:/home/pycsw/VERSION.txt --volume ${PWD}/LICENSE.txt:/home/pycsw/LICENSE.txt --volume ${PWD}/COMMITTERS.txt:/home/pycsw/COMMITTERS.txt --volume ${PWD}/CONTRIBUTING.rst:/home/pycsw/CONTRIBUTING.rst --volume ${PWD}/pycsw/plugins:/home/pycsw/pycsw/plugins --volume ${PWD}/aahll.cfg:/etc/pycsw/pycsw.cfg --volume ${PWD}/db-data:/home/pycsw/db-data/ --publish 8000:8000 aahll-pycsw --reload
```

- now pycsw is running on http://192.168.99.100:8000

- for the map open a new console and navigate into the MapSearch Folder 
- then add the following:

```
python webmap.py
```

Installation on MacOS
---------------------------

- open the Terminal 
- navigate in the pycsw folder in the cloned repository
- add the following (just completely copy and paste):

```
docker run --name pycsw-dev --volume ${PWD}/pycsw:/usr/lib/python3.5/site-packages/pycsw --volume ${PWD}/docs:/home/pycsw/docs --volume ${PWD}/VERSION.txt:/home/pycsw/VERSION.txt --volume ${PWD}/LICENSE.txt:/home/pycsw/LICENSE.txt --volume ${PWD}/COMMITTERS.txt:/home/pycsw/COMMITTERS.txt --volume ${PWD}/CONTRIBUTING.rst:/home/pycsw/CONTRIBUTING.rst --volume ${PWD}/pycsw/plugins:/home/pycsw/pycsw/plugins --volume ${PWD}/aahll.cfg:/etc/pycsw/pycsw.cfg --volume ${PWD}/db-data:/home/pycsw/db-data/ --publish 8000:8000 aahll-pycsw --reload
```

- now pycsw is running on localhost:8000

- for the map open a new console and navigate into the MapSearch Folder 
- then add the following:

```
python webmap.py
```

Installation in Linux
-----------------------------

- we never test it, but we think it is similar to the installation for MacOS 

Important for every calculation (except with the batch file)
------------------------------------------------------------------

Sometimes when you want to start the container again, you have to add the following first, to remove the old container:

```
docker rm -f pycsw-dev
```

Run image from docker-hub
==============================

Open a console (PowerShell, DockerToolbox, Terminal) and add the following:

```
docker run -p 8000:8000 ani18/aahll-pycsw
```

At the moment, it is not possible to do a transaction. You can only do requests for the saved data in the database. We are working on this problem. 
    
API Testsuite
================

Test directly in Browser  
---------------------------

The following endpoints are examples which can be added in the browser when pycsw is correctly running:

GetSimilarRecords:

http://localhost:8000/?service=CSW&version=2.0.2&request=GetSimilarRecords&id=aahll:1

http://localhost:8000/?service=CSW&version=2.0.2&request=GetSimilarRecords&id=aahll:1,aahll:2

http://localhost:8000/?service=CSW&version=2.0.2&request=GetSimilarRecords&id=aahll:1,aahll:2&similar=10

http://localhost:8000/?service=CSW&version=2.0.2&request=GetSimilarRecords&id=aahll:1,aahll:2&similar=10&outputformat=application/json

http://localhost:8000/?service=CSW&version=2.0.2&request=GetSimilarRecords&id=aahll:1,aahll:2&similar=10&outputformat=application/xml

Infos: 

- you can use the ids from aahll:1 to aahll:18
- the similar parameter has a range from 1 to 50
- you can add the outputformat=application/xml or outputformat=application/json paramter behind every endpoint, default is json


GetSimilarityBBox:
----------------------

http://localhost:8000/?service=CSW&version=2.0.2&request=GetSimilarityBBox&idone=aahll:8&idtwo=aahll:9

http://localhost:8000/?service=CSW&version=2.0.2&request=GetSimilarityBBox&idone=aahll:1&idtwo=aahll:2

Infos: 

- You can use the ids from aahll:1 to aahll:18
- you can add the outputformat=application/xml or outputformat=application/json paramter behind every endpoint, default is json


Test with Postman
===================

Postman can help us to show how the API-Endpoint is working. Download the JSON-file from the Postman folder and start Postman. Then you have to click import, to import the file 'Api_Tests.postman_collection.json'. Then you can choose the API-File from the left side and see which URL's are added. Under "params", the keys are listed. So the URL will be splitted up in parts and in the table of "params" you can see the information behind the keys. The GET - Requests have also tests. This test includes the statuscode, the right content-type (json) and the response time which should be under 5 seconds.





