# Implementing push notifications on Ionic
Upon request from people in the community, I put together a simple guide for how to implement push notifications on Ionic. Me myself had issues with Ionic.io where the push notifications where queued but never received.
This is not a tutorial on how to implement push notifications, so you will need to know the basics of how push notifications work.

This works on iOS as well as on Android.
If you want to add or change any information here, you're more than welcome to create a pull request!

##1. Install required plugins
Install the Cordova push plugin to enable access to the push API from Cordova. The value of `SENDER_ID` maps to the project number in the Google Developer Console.
    cordova plugin add phonegap-plugin-push --variable SENDER_ID="XXXXXXX"

##2. Register for push notifications
Put this in the callback (1st argument) of the `angular.module('appName',[...]).run` method.

    $ionicPlatform.ready(function() {
        var push = PushNotification.init({
            android: {
                senderID: "XXXXXXX"
            },
            ios: {
                alert: true,
                badge: true,
                sound: true
            },
            windows: {}
        });
    });
    
##3. Handle push register success
Send the push token to your backend. This push token is used for pushing data to the device. Use the `push` variable declared in the previous step.

    push.on('registration', function(data) {
      var token = data.registrationId;

      $http.post("https://myapi/register_push", {
        device_token: token,
        platform: ionic.Platform.isIOS() ? 'ios' : 'android'
      });
    });
    
##4. Handle push notification received
Use the `push` variable declared in step 2.

  	push.on('notification', function(data) {
  	  $ionicPopup.alert({
  	    title: 'Message!',
  	    template: data.message
  	  });
  	});
  	
##5. Send message to APNs and GCM servers
You could do this in a ton of different ways depending on your backend. Me myself I use Laravel, and use a great Composer package by Davi Nunes to make sending push notifications easier: https://github.com/davibennun/laravel-push-notification

##6. Did it work?
Did you get it up and running? Congrats!

Do you have issues getting it working on Android? Make sure GCM is correctly configured.
Do you have issues getting it working on iOS? Make sure you build the app with the right provisioning profile (enabled for push notifications of course), and that you push with the right APNS certificate.
