//############ in app.js :

// test for iOS 4+
function isiOS4Plus() {
  if (Titanium.Platform.name == 'iPhone OS') {
		var version = Titanium.Platform.version.split(".");
		var major = parseInt(version[0]);
		// can only test this support on a 3.2+ device
		if (major >= 4) {
			return true;
		}
	}
	return false;
}

if (isiOS4Plus()) {

	var service;
	// Ti.App.iOS.addEventListener('notification',function(e){
	// You can use this event to pick up the info of the noticiation.
	// Also to collect the 'userInfo' property data if any was set
	// Ti.API.info("local notification received: "+JSON.stringify(e));
	// });
	// fired when an app resumes from suspension
	Ti.App.addEventListener('resume', function(e) {
		Ti.API.info("app is resuming from the background");
	});
	Ti.App.addEventListener('resumed', function(e) {
		Ti.API.info("app has resumed from the background");
		// this will unregister the service if the user just opened the app
		// is: not via the notification 'OK' button..
		if (service != null) {
			service.stop();
			service.unregister();
		}
		Titanium.UI.iPhone.appBadge = null;
	});
	Ti.App.addEventListener('pause', function(e) {
		Ti.API.info("app was paused from the foreground");
		service = Ti.App.iOS.registerBackgroundService({
			url : 'bg.js'
		});
		Ti.API.info("registered background service = " + service);
	});
}

//############## end app.js changes

//############# in bg.js :

Ti.API.info("hello from a background service!");

var alertCount = 0;
var notification = null;

function notify(resp) {
	// This creates the notification alert on a 'paused' app
	notification = Ti.App.iOS.scheduleLocalNotification({
		alertBody : resp,
		alertAction : "OK",
		userInfo : {
			"hello" : "world"
		},
		badge : alertCount,
		date : new Date(new Date().getTime() + 10)
	});
}

function checkFeed() {
	// silently ignore this if there's no network connection
	if (Titanium.Network.online == false) {
		return;
	}
	var t = new Date().getTime();
	Ti.API.info('checking feed in bg.. ' + t);

	var xhr = Titanium.Network.createHTTPClient();
	xhr.timeout = 1000000;
	xhr.onerror = function(e) {
		Ti.API.info('IN ERROR ' + e.error);
	};
	xhr.onload = function() {
		// demo to increase the badge number...
		alertCount++;
		var response = this.responseText;
		Ti.API.info("the reply was: " + response);
		// open the notification
		notify(response);
	};
	xhr.open('GET', 'http://YOUR_SERVER.COM/service_test.php');
	xhr.send();
	/*
	 my service_test.php is simply:
	 <?php
	 echo "hello from a server at: ".date("r");
	 ?>
	 */
}

Ti.App.iOS.addEventListener('notification', function() {
	Ti.API.info('background event received = ' + notification);
	Ti.App.currentService.stop();
	Ti.App.currentService.unregister();
});

// Kick off a timer to trigger a function called 'checkFeed' every 10 seconds (= 10000 ms)
var timer = setInterval(checkFeed, 10000);

//####### END bg.js
