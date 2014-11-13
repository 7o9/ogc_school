. 

Metaspatial WMS 1.3 Server 
===========================


About this Page
---------------

This page has been compiled as a quick reference and introduction to the OGC WMS interface and service standard. All map images on this page are dynamically requested by standard OGC WMS queries and rendered by the live OGC Demo Server operated by `metaspatial.net <http://metaspatial.net>`_. This page does **not** describe all the features and options the standard offers. Please find the full technical description of the OGC WMS standard on the web sites of the Open Geospatial Consortium at: http://www.opengeospatial.org/standards/wms 

End Point
----------

The end point to access this service is: http://metaspatial.net/cgi-bin/ogc-wms.xml. The OGC WMS Capabilities document contains all information required to make an educated request. It can be retrieved by sending a `getCapabilities <http://metaspatial.net/cgi-bin/ogc-wms.xml?REQUEST=GetCapabilities&SERVICE=WMS&VERSION=1.3>`_ request to the server. 

Current information about the status of this server can be found at the `metaspatial wiki <http://arnulf.us/OGC_WMS_Demo_and_Reference_Server>`_.


Note
~~~~
If this endpoint is called without any additional parameters the software running on this server (`MapServer <http://mapserver.org>`_) will return the following text: 

``No query information to decode. QUERY_STRING is set, but empty.``

This is documented and expected behavior. The server did not get any instructions what to do and therefore only informs us that it has nothing to do. Other software may return different or even no information at all.

In order to query maps from this server we have to first get the metadata. This metadata is contained in the ''OGC WMS Capabilities Document'' and comes in XML format. It is requested by calling: 

``http://metaspatial.net/cgi-bin/ogc-wms.xml?REQUEST=GetCapabilities&SERVICE=WMS&VERSION=1.3``

This document contains all the information needed to make a valid OGC WMS GetMap request.

Getting a Map Image
-------------------
The ``GetMap`` request queries the server with a set of parameters describing the map image. The values of the parameters are taken from the Capabilities document (see above). A correctly formulated ``GetMap`` request will create the image shown below. 

*The URL of this link has been trunkated for better readability.*::

	http://metaspatial.net/cgi-bin/ogc-wms.xml?
	VERSION=1.3.0&
	REQUEST=GetMap&
	SERVICE=WMS&
	LAYERS=DTM,Overview,Raster_250K,Topography,nationalparks,Infrastructure,Places&
	STYLES=,,,,,,&
	CRS=EPSG:27700&
	BBOX=424735.97883597884,96026.98412698413,467064.02116402116,127773.01587301587&
	WIDTH=400&
	HEIGHT=300&
	FORMAT=image/png&
	BGCOLOR=0xffffff&
	TRANSPARENT=TRUE&
	EXCEPTIONS=XML

Use the link: `GetMap <http://metaspatial.net/cgi-bin/ogc-wms.xml?VERSION=1.3.0&REQUEST=GetMap&SERVICE=WMS&LAYERS=DTM,Overview,Raster_250K,Topography,nationalparks,Infrastructure,Places&STYLES=,,,,,,&CRS=EPSG:27700&BBOX=424735.97883597884,96026.98412698413,467064.02116402116,127773.01587301587&WIDTH=400&HEIGHT=300&FORMAT=image/png&BGCOLOR=0xffffff&TRANSPARENT=TRUE&EXCEPTIONS=XML>`_ to retrieve the map image form the OGC Demo and Reference Server. It will be rendered dynamically each time you request the image (given that no proxy interferes and delivers an earlier graphic rendition of the map data).

	.. image:: images/metaspatial.net_GetMap.png
		:width: 400
		:height: 300
		:scale: 100
		:alt: Map image returned by the OGC WMS server

Layers
------

WMS can be structured into layers. Each layer can be queried individually or in combination with another layer. Layers will be drawn in the order requested, the first at the bottom of the image and eacht layer stacked on top. If an opaque layer is rendered at the top of the image it may cover all the other layers. 

Every server has one root layer which contains the name of the server, here it is: ``OGC WMS Reference Server``. This server has seven map layers:

1. DTM (Digital Terrain Model)
2. Overview (Overview 1:1m)
3. Raster_250K (Raster 1:250k)
4. Topography (selection of topographical data derived from OS VectorMap District 1:50k)
5. National Parks (The national parks of Great Britain)
6. Infrastructure (selection of topographical data derived from OS VectorMap District 1:50k)
7. Named Places (selection of topographical data derived from OS VectorMap District 1:50k)

In the following request the layer "Overview" has been taken out of the request but otherwise the request is unchanged. It reveals the digital terrain model (DTM), a layer that was previously invisible.

	.. image:: images/metaspatial.net_GetMap_opaque.png
		:width: 400
		:height: 300
		:scale: 100
		:alt: Map with translucent overlay

Note
~~~~

Most server implementations will always render all requested layers regardless of whether they are visible in the final map image or not. In the above example the Digital Terrain Model of Great Britain will always be rendered even if the overview map completely hides it. If you want to reduce the load on your server you might want to avoid this by combining layers in a more useful way or setting up a separate WMS for each content. Always remember that the OGC WMS interface standard gives you a lot of freedom to experiment and create an optimized set of services and layers for your content. But it needs a lot of experimentation and thought to get there. Strictly adhering to the standard will ensure that your service will be interoperable but it does not guarantee optimized performance. This is where your know-how and skilful means are needed. 

Transparency / Translucency
---------------------------

The map image can be overlayed with maps from other servers. In those cases it may be helpful to request the top level image in a format which supports a transparency such as the aplha channel. Then we can see what is below this map image. 

The following examples show how each layer can be transparent and reveal what is underneath.

	.. image:: images/GetMap_opaque.png
		:width: 400
		:height: 300
		:scale: 100
		:alt: Map with translucent overlay

This request additionally requests the DTM layer which adds the black to grey shading:

	.. image:: images/GetMap_translucent.png
		:width: 400
		:height: 300
		:scale: 100
		:alt: Map with translucent overlay

If we look at the examples in the section above we can see a green shade, this is the layer "National Parks". It is translucent. This means that it just adds a shade of green to the image - or rather all the layers "below" the "National Parks". This is a setting of the server which we cannot change. But many clients can change this setting in the image that they have received from the server on their own. This can be done with PNG but not with the JPEG format.

Scale Limits
------------

It is important to understand that every layer can have scale limits when it is not displayed. For example it would not make sense to display the "Overview" and "Raster_250K" at the same time because they contain the same data but with different levels of generalization. So instead they have mutually exclusive scale limits so that they will never be returned in one image. The scale limits are set by the server and do not have to be configured by the client.

All this information is contained in the Capabilities document.

Zooming In and Out
------------------

The client can "zoom in" and "zoom out" of the map by calculating new values for the BBOX (bounding box) parameter. The same applies to all map navigation functions like panning or selecting a completely new area.

It is important to note that the server only returns an image to the client, but no additional information about the size, coordinate system, scale, etc. Therefore the server must make sure that it also returns images which may look "wrong" because the client has requested a 'squashed' image (for example with a width to height ratio other than 1:1). If the server would return anything other than exactly what was requested then the client will base its next request on wrong parameters. 

The following image shows the same area as above but with the different parameters for the WIDTH which has been changed from 400 pixels in the example above to 200 pixels here. ::

	...
	WIDTH=200&
	HEIGHT=300&
	...

The resulting image covers the same area as defined by the bounding box coordinates of the request but the image geometry is out of proportion. 

	.. image:: images/GetMap_squashed.png
		:width: 200
		:height: 300
		:scale: 100
		:alt: Map image with changed width-to-height ratio

Error Messages
--------------

In case the client causes an error by formulating a wrong request the server will return an error message. To demonstrate this we will request for a map with a layer named "Underview" (which does not exist on the server).

Typically the server will return an error message like this: ::

	<?xml version='1.0' encoding="ISO-8859-1" standalone="no" ?>
		<ServiceExceptionReport version="1.3.0" xmlns="http://www.opengis.net/ogc" 
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
		xsi:schemaLocation="http://www.opengis.net/ogc
		http://schemas.opengis.net//wms/1.3.0/exceptions_1_3_0.xsd">
		<ServiceException code="LayerNotDefined">
			msWMSLoadGetMapParams(): WMS server error. 
			Invalid layer(s) given in the LAYERS parameter. 
			A layer might be disabled for this request. 
			Check wms/ows_enable_request settings.
		</ServiceException>
	</ServiceExceptionReport>

`Note that differrent software may return different text messages along the ``ServiceException code``. `

One problem may be that the client cannot display this message in the image display element. An HTML client in a browser would simply show the above message as a broken image, something like this:

	.. image:: images/broken-image.png
		:width: 23
		:height: 27
		:scale: 100
		:alt: Crossed out image: This is a typical symbol displayed by browsers for content that cannot be rendered as a graphic image. 

To prevent this from happening we can change the format of the error message (the EXCEPTIONS parameter) to INIMAGE. In that case we can see the error message printed into an image:

	.. image:: images/ogc-wms_in-image_error.png
		:width: 500
		:height: 300
		:scale: 100
		:alt: Error message printed into an image file

Note that the image will have exactly the pixel size that was requested by the client. 

Getting Information about Objects "on" the Map
----------------------------------------------

The OGC WMS standard offers an optional request type which allows the user to query the server for alphanumerical information. The name of the request is ``GetFeatureInfo`` and the functionality is limited to serve the needs of a simple "click into the map". Implementing the feature in a client is straightforward but requires some background understanding. 

Note
~~~~

As the name ``GetFeatureInfo`` already suggests the server will not return geographic feature(s) but only selected alphanumerical information. For more functionality please refer to the OGC WFS standard which allows to compose complex queries for features including a geographic selection, alphanumeric values and so on. 

The ``GetFeatureInfo`` Request
------------------------------

Before querying for objects we need to see a map. We use the GetMap request to get a map image: ::

	http://metaspatial.net/cgi-bin/ogc-wms.xml?
	VERSION=1.3.0&
	REQUEST=GetMap&
	SERVICE=WMS&
	LAYERS=Overview,Raster_250K,nationalparks,Topography,Infrastructure,osm_points&
	WIDTH=400&
	HEIGHT=300&
	CRS=EPSG:27700&
	BBOX=427966.6666666667,106800,431833.3333333333,109700&
	FORMAT=image/png&
	EXCEPTIONS=XML

Teh result will look like this: 

	.. image:: images/new-forest_national-park_map.png
		:width: 400
		:height: 300
		:scale: 100
		:alt: A map image from the New Forest National Park in Great Britain. 

In the next step the user has to click into the map. Let's assume the user has clicked the position: ::

	X=231
	Y=280

Then the client will create a ``GetFeatureInfo`` request and submit all the informaion needed by the server to decode this request: :: 

	http://metaspatial.net/cgi-bin/ogc-wms.xml?
	VERSION=1.3.0&
	REQUEST=GetFeatureInfo&
	SERVICE=WMS&
	LAYERS=osm_points&
	QUERY_LAYERS=osm_points&
	WIDTH=400&
	HEIGHT=300&
	CRS=EPSG:27700&
	BBOX=427966.6666666667,106800,431833.3333333333,109700&
	INFO_FORMAT=text/html&
	EXCEPTIONS=XML
	&X=231&
	Y=280

Note that the URL contains information which we do not expect ot need, for example the pixel size of the map. The size of the map is required for the server to decode the click position and then transform it into real world coordinates by using the BBOX (Bounding Box) parameter. Only then can the server query it's database for features. 

If it finds anything it will return them in the requested format (here an HTML file). In this example the server finds a pub called "The Crown Stirrup" and a bus stop named "Ye Old Crown and Stirrup".  

	.. image:: images/GetFeatureInfo_result.png
		:width: 305
		:height: 107
		:scale: 100
		:alt: Result of a GetFeatureInfo request. 

Note 
~~~~

The OGC WMS standard does not specify what kind of a text or HTML file is returned as a result of a ``GetFeatureInfo`` request. The server will create the HTML file as it has been set up by the site operator.

``GetFeatureInfo`` result as GML
--------------------------------

If you need more structured information the best bet is to request for a GML file. 

	.. image:: images/ogc-wms_getfeatureinfo_result.gml.png
		:width: 305
		:height: 107
		:scale: 100
		:alt: Result of a GetFeatureInfo request. 

To get the informaiton in the GML format simply change the parameter ``INFO_FORMAT`` to read: ::

	INFO_FORMAT=text/html&

Legends
-------

The last feature that will be touched on in this short introduction are legends. The OGC WMS Standard specifies a request called ``GetLegendGraphic``. It requests images for each layer of a WMS. The style of the returned image largely depends on how the software is implemented. The following example requests for a legend image of the national parks. The request looks as follows: ::

	http://metaspatial.net/cgi-bin/ogc-wms.xml?
	version=1.3.0&
	service=WMS&
	request=GetLegendGraphic&
	sld_version=1.1.0&
	layer=nationalparks&
	format=image/png&
	STYLE=default

The result shows a legend item with the color of the areas covered by National Parks in the map (see above images) and a text string with the layer name.

	.. image:: images/national-parks_legend.png
		:width: 110
		:height: 22
		:scale: 100
		:alt: Legend image for the National Parks layer. 

Note
~~~~

Every server will return different looking legends. This makes it hard to impossible to create a homegeneous legend by using generic requests to different servers. Therefore especially dynamic clients must be aware that there are many different ways this feature can be implemented. 
