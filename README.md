Purpose:
-----------------

Provide an http based API that you can send POST requests to and it will send a push notification to apple's notification service.

Prerequisites
-----------------

* Install [node](http://nodejs.org/)
* Install [npm](http://npmjs.org/)
* Install [couchdb](http://couchdb.apache.org/)

Goal Usage:
-----------------

     node index.js
     curl -d "appId=<your-app-id>&appSecret=<your-app-secret>&deviceToken=760ff5e341de1ca9209bcfbd320625b047b44f5b394c191899dd5885a1f65bf2&notificationText=What%3F&badgeNumber=4&sound=default&payload=5+and+7" http://localhost:3000/

Getting Started:
-----------------

* Start couchdb
* Create a node_apn database in couch
* Create the pre-requisite data needed to support the server
 * Add the couch\_views/users\_all.js content as a view called "\_design/users" with the name "all"
 * Add a user document and configure that user's applications
 * There is an example document with one user and one application configured shown below
 * Pay attention to the certData and keyData entries in the settings hash, the server currently provides a way for you to upload the certificate and key, but it's ugly. The plan is to possibly use a different front end to manage the things that might be easier to do in a blocking environment.

--

     git clone git@github.com:adamvduke/node-apn-server.git
     cd node-apn-server
     npm install
     node index.js

Example Document:
----------------

     {
      "_id": "0aead832d8a8e5f57e0c10b5ff000565",
      "_rev": "11-92625b691c7e8203250de95fea65ada2",
      "username": "adamvduke",
      "password": "password",
      "type": "user"
      "applications": [
       {
        "app_id": "1",
        "app_secret": "1",
        "settings": {
                     "certData": "Your Cert Data goes here",
                     "keyData": "Your Key Data goes here",
                     "gateway": "gateway.push.apple.com",
                     "port": 2195,
                     "enhanced": true,
                     "cacheLength": 5
                    }
       }
      ]
     }

Sending Notifications:
----------------------

There are three required parameters:

* appId - Your application's appId
* appSecret - Your application's appSecret
* deviceToken - The device token to send the notification to

Optional parameters are:

* notificationText - The text that will display on the device.
* badgeNumber - The value of the badge to be set on the application's icon.
* sound - The sound to be played with the notification
* payload - Extra data to included in the notification, formatted as a json dictionary
 * Passed in the options dictionary, with the key: info, during -application:didFinishLaunchingWithOptions: 

Certificates:
-----------------

* Do the dance to get the production push notification certificate from the iOS provisioning portal
* Download and install the certificate into Keychain Access.app
* Export the certificate and private key separately as apns-prod-cert.p12 and apns-prod-key.p12 respectively
* For both exports, you will be asked to specify a password, then asked for your keychain password. Do not specify a password on the first prompt.
* Run the following commands to convert the certificate and private key to .pem format

--

    openssl pkcs12 -clcerts -nokeys -out apns-prod-cert.pem -in apns-prod-cert.p12
    openssl pkcs12 -nocerts -out apns-prod-key.pem -in apns-prod-key.p12

You will be forced to set a PEM passphrase on the second command, so execute the following command to remove it:

    openssl rsa -in apns-prod-key.pem -out apns-prod-key-noenc.pem

See [this blog entry](http://blog.serverdensity.com/2010/06/05/how-to-renew-your-apple-push-notification-push-ssl-certificate/) for more details on setting up the certificates.

Credits:
-----------------

* [node-apn](https://github.com/argon/node-apn) by [Andrew Naylor](https://github.com/argon)
* [node-querystring](https://github.com/visionmedia/node-querystring) by [visionmedia](https://github.com/visionmedia/)
* [How to renew your Apple Push Notification Push SSL Certificate](http://blog.serverdensity.com/2010/06/05/how-to-renew-your-apple-push-notification-push-ssl-certificate/) by [David Mytton](http://blog.serverdensity.com/author/dmytton/) 
