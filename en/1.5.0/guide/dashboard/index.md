---
layout: post
title: Dashboard
---

What is it ?
------------

The [dashboard](http://apisense.io/) is the main interface to all data collectors. Using it you will be able to:
	
* [Create your own crops](#manage-crops)
* [Retrieve and analyze fresh data](#retrieve-your-data)


## Manage crops

### Create

Then you have to create a collect and set its name, description and optionaly your website.
Those information will be used in our store to explain what you are planning to do with this collect.
Again these are really important and must be filled carefully.
Keep in mind that if your crop is _Public_ or _Unlisted_, anybody will be able to contribute from our application [Bee](../bee).

<div class="alert alert-warning" role="alert">WARN: By default your collect is private so people won't be able to see it until you finish to write it. Remember to set it visible once it's ready!</div>
<div class="alert alert-info" role="alert">INFO: A unique identifier is created for your collect, it should look like PzeDd1r0nNtN8JdNNrlY and can be found in the details menu of your collect.</div>

### Code it
Last crutial step, you have to write your crop's script using every [Sting](../../stings) you need.
It should be quite easy and user friendly but if you're in trouble, you can contact us at [contact@apisense.io](contact@apisense.io)

### Publish it
First of all you have to create an account.
Some personal information like your username will be linked to your collects and shown in our store.
Choose it wisely!

## Handle private crop

If you want to create crops only accessible by your community,
you will need to configure it as a _Private_ crop,
which will be invisible and unusable for every APISENSE sdk instances other than yours.

### Create your own application

To configure the APISENSE SDK as needed to use your private crop, you will need to create an application using the APISENSE sdk, [as documented here](../sdk).

### Enable your APISENSE sdk

Don't forget to reference the application on your [dashboard](../dashboard) profile, in the submenu _Mobile Applications_,
this will generate a key enabling your sdk instance to contact the APISENSE server, [see how to set it in your application](../sdk#authorize-your-sdk).

### Add access to your crops

You will have to set the access key referencing your account to grant access to your crops.
Open your profile settings and copy the key from the field _Read only API Key_.
See [how to use the key in your sdk](../sdk#add-your-private-key).


## Retrieve your data

### Get everything

To retrieve the data from your collect, you can click on the button `Download data` from the dashboard.
But you also have access to an API to get the raw json using `/api/v1/$collectIdentifier/data`.

The Json you will retrieve is build as follow:

    [
      {
        "metadata": {
          "timestamp": "2015-10-06T16:00:43+02:00",
          "device": "Android"
        },
        "header": {
          "environmentalInfo": "from sync(...) method, 1 per upload"
        },
        "body": [
          { "yourTrace": "from save(...) method, 1 per trace" },
          { "yourTrace": "another saved trace" },
        ]
      }, {
        ...
      }
    ]

### Filter uploaded data

If you want to insert some specifics, calculated values,
you can add a pre-upload filter, applied on each uploaded data (see the section above for the syntax).

    // This filter will process every uploaded data
    rest.setPreUploadTreatment(function(data) { // data will be the uploaded JSON
       var myUploadedDataArray = data.body;
       var myMetadata = data.metadata;
       myMetadata.count = myUploadedDataArray.length;
       return data; // It is recommended to send back the same json structure as the input.
    });

### Filter output

If you don't want to download every collected traces from the server, you can define custom routes to filter the data beforehand.

The creation of these routes can be done in the `Filters` menu, from the collect dashboard.
In this menu, you will find a script editor, in which you can add routes with the following (Javascript) syntax:

    // This filter will only return the metadata of each collected trace.
    // It can be accessed from the route /api/v1/$collectIdentifier/data/meta
    rest.prepareFilter("meta", function(data){ // data will be your raw data json array
      var result = [];
      for each (var ele in data) {
        result.push(ele.metadata);
      }
      return result; // This will be parsed as a Json.
    });


After saving your script, you can access the result from the route `/api/v1/$cropIdentifier/data/meta`.

## Retrieve your media

Some stings will record media files (like pictures, sound, or video).
For obvious reasons, those media will not directly be saved in the data JSON,
but an identifier will be set instead.


### Metadata format:

You can retrieve medatada for every media uploaded on a crop from the route `/api/v1/$cropIdentifier/media`.
Every metadata object will contain the following elements:

    {
      "cropIdentifier": "Zz86D0v6O1CWGldoD5Zg",
      "identifier": "64C8356C-5C91-48E4-AE5D-E88F3AF047A8",
      "size": 142222,
      "upload": 1455618401229,
      "contentType": "image/jpeg",
      "url": "/api/v1/Zz86D0v6O1CWGldoD5Zg/media/64C8356C-5C91-48E4-AE5D-E88F3AF047A8"
    }

With:

- `identifier`: Media identifier set in your sting result.
- `size`: Media size in bytes.
- `upload`: Upload timestamp.
- `contentType`: Mime type found by the honeycomb (may be null if the type isn't recognized).
- `url`: Media location on the honeycomb.

### Retrieve the media file

A raw media file can be retrieved using the route `/api/v1/$cropIdentifier/media/$mediaID`,
where mediaID is the media identifier set in the sting result.

The file will be sent via an `application/octet-stream` content type,
whichever the file type may be.
