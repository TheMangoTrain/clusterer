Clustering in Maps v2 for Android
=================================

This is a working example that serve as proof of concept of poi clustering in Android Maps v2. 

Getting started
---------------

Of course, you must have a working **Google Play Services library** setup in your project, and do all this working from a **MapFragment** or wherever you have your **GoogleMap** object.

You have all the files you need in the [src/in/nlopez/clustering](https://github.com/mrmans0n/android-maps-v2-clustering/tree/master/src/in/nlopez/clustering) directory of the example project. **Just drop them in your project, and you're good to go.**

The objects you want to display in the map **must implement the Clusterable interface**. That interface only needs one method, getPosition() that returns a LatLng object (latitude and longitude).

An example:
```java
public class MyPoi implements Clusterable {
	private String name;
	private LatLng position;

	public MyPoi(String name, LatLng position) {
		this.name = name;
		this.position = position;
	}

	public String getName() { return name; }

	@Override
	public LatLng getPosition() { return position; }
}
```

In the class you are using the GoogleMap object, you must instance the **Clusterer** object specifying also the clusterable object.

```java
	GoogleMap map;
	Clusterer<MyPoi> clusterer;

	[…]

	private void initClusterer() {
		clustered = new Clusterer<MyPoi>(this, map);		
	}

```

The **Clusterer** will control your zoom and will reload the cluster markers when the zoom changes without your interaction.

Customizing your markers
------------------------

You can customize the marker's appearance, even if it's a cluster marker. You have **two listeners** defined with interfaces:

* OnPaintingMarkerListener, that will help you with your points of interest.
* OnPaintingClusterListener, that will help you with the cluster points.

The listeners will be invoked when the markers are being generated by the **Clusterer** and will be able to provide the markers with the **MarkerOptions** data we want.

```java
		clusterer.setOnPaintingMarkerListener(new OnPaintingMarkerListener<MyPoi>() {

			@Override
			public void onMarkerCreated(Marker marker, MyPoi clusterable) {
							markers.put(marker, clusterable);
			}

			@Override
			public MarkerOptions onCreateMarkerOptions(MyPoi clusterable) {
				MyPoi poi = (MyPoi) clusterable;
				return new MarkerOptions().position(clusterable.getPosition()).title(poi.getName()).snippet(poi.getDescription());
			}
		});

		clusterer.setOnPaintingClusterListener(new OnPaintingClusterListener() {

			@Override
			public void onMarkerCreated(Marker marker, Cluster cluster) {
				clusters.put(marker, cluster);
			}

			@Override
			public MarkerOptions onCreateClusterMarkerOptions(Cluster cluster) {
				return new MarkerOptions()
						.title("Clustering " + cluster.getWeight() + " items")
						.position(cluster.getCenter())
						.icon(BitmapDescriptorFactory.fromBitmap(getClusteredLabel(Integer.valueOf(cluster.getWeight()).toString(),
								MainActivity.this)));
			}
		});

	}
```

Screenshots
-----------

![Clustering example 1](http://nlopez.in/uploads/admin/image/image/25/clusterer_1.png)
![Clustering example 2](http://nlopez.in/uploads/admin/image/image/25/clusterer_1.png)

Contributing
------------

You know the drill: fork it and then make a pull request, or just put an issue if you find something!

Contact
=======

You can follow me on twitter at [@mrmans0n](http://twitter.com/mrmans0n) and email me at nacho@nlopez.in

Also, my site has a lot of info you might find useful: http://nlopez.in

License
=======

    Copyright 2013 Nacho Lopez

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.