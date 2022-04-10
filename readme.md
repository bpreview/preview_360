# 360 Gallery

This project was done in a few day in a van in norway to share my 360 degrees photos with family and friends, as I didn't find any service providing such functionality.
It was my first app using flask, and I had a limited time, so things may be not very clean but hey, it works.


# How to build

Make sure you are in the right directory (Dockerfile directory) and run :
```
 docker build -t 360gallery .
```

# HOW TO RUN

you will need a token to use admin features (yeah, I didn't have time & knowledge at this time to implement a login feature) the default token is :
> 6269656e207675206c2761727469737465

You can use you own token by using an environment variable nammed "TOKEN" at launch.
To launch, use the following command:
```
docker run -p <port>:80 -e TOKEN=<YOUR_TOKEN> --name 360gallery -v <LOCAL_STORAGE_PATH>:/app/static 360gallery
```
You will then need to access the init url to initialize folders and databases (this is needed only on first run, not on subsequent runs if you use a local storage to keep files between restarts)
the url is the following:
> \<hostname>:\<port>/init

Example on localhost with port 8080:
> localhost:8080/init

You will be redirected to homepage after that.

# Available pages:

Every admin action takes place using urls, there is no button because, hey, I had not much time and no auth module, so they would have been visible to everyone.
If a token is needed, you have to append the token to your base url using:

``` 
?token=<YOUR TOKEN>
```


|Function |Base url | Need token ?|
|--|--|--|
| Upload photo | /upload | Yes  |
| Analytics | /analytics | Yes |
| Gallery | /gallery | No |
|Init|/init|No|
|DB edit|/db_edit|Yes|
|Viewer|/viewer/<photo_path> | No
|Remove photo | /remove/<photo_path> | Yes

Example with localhost, port 8080, and accessing upload page with default token:
> localhost:8080/upload?token=6269656e207675206c2761727469737465

## Upload

![Upload file](https://www.zupimages.net/up/22/14/60th.jpg)

You can input a location manually, or let the server find it by itself if your photo is geolocated.

When uploading, the server may seem to freeze, this is expected as every upload will attempt geotagging using geopy if there is exif coordinates, and generate the files to render in a sphere, let it do it work, you will be redirected to the gallery after the processing is completed. 

If geotagging failed, it will default to "Not specified". You can manually edit the place later in DB Edit.

## Analytics

You can access logs of the server by using the analytics path and token. The server returns a string of array of dicts, for each page loading, there is a dict entry in the array, following this format:
> {'url': 'ht<span>tp://<span>localhost:18888/gallery', 'user_agent': {'browser': None, 'language': None, 'platform': None, 'version': None}, 'blueprint': None, 'view_args': None, 'status': 200, 'remote_addr': '172.17.0.1', 'xforwardedfor': None, 'authorization': False, 'ip_info': '{"businessName": "", "city": "", "continent": "", "country": "", "countryCode": "", "ipName": "", "ipType": "", "isp": ""}', 'path': '/gallery', 'speed': 0.021239, 'date': datetime.datetime(2022, 4, 10, 19, 33, 21), 'username': None, 'track_var': None}

Honestly, there is a lot of fields here that I did not investigate and may not work here, since I used flask_track_usage without any tweaking, but it's better than nothing.


## Gallery
![Gallery](https://www.zupimages.net/up/22/14/xvgj.jpg)
This is an example of your gallery.

(The title here is 360Norway since I done this for my trip to Norway, but you can edit "templates/gallery.html" to put any name you want.)

You can click the city name to be redirected to google maps. It will use the coordinates of the photo, so even if geotagging failed, you can still click on it and be redirected to the exact coordinates the photo was taken.

If you clic on the photo, you will be redirected to the viewer to display it in 360 mode.


## Init

Init page does not display anything, it only create the folder structure and databases. It does not do anything if everything is already initialized. You will be redirected to gallery on completion.

## DB edit
![DB edit](https://www.zupimages.net/up/22/14/ymun.jpg)
You can use this page to change the place name and coordinates of photos. Change the field value and clic on submit to update it.

## Viewer
![Viewer](https://www.zupimages.net/up/22/14/s7hi.jpg)The viewer allows you to navigate in the photo. You can use the bottom images to quickly access the two photos after or before the current one.

On supported platforms (it should work on android at least, i have not tested it on other devices) a compass icon will appear under the fullscreen icon on the top left hand side. You can click it to enable gyroscope navigation and move your device to navigate the photo.

## Remove photo

The remove photo allows you to easily remove an uploaded photo without having to do it manually. To do it quickly, without worrying about the path (which is not only it's name), preview the photo and replace 'viewer' with 'remove', and append your token.
