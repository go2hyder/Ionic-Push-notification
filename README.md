# Ionic-Push-notification

<html>
<style>
code,pre{
	padding: 5px;
	background: #efefef;
}
b{
	font-size: 14px
}
</style>
<i>Link:- <a href="https://docs.ionic.io/setup.html#installation" target="_blank">https://docs.ionic.io/setup.html#installation</a></i><br>
<i>There are two steps.first one is register your device to ionic cloude and second one is connect cloude to FCM or APN server</i>

<h2><u>Connect App to ionic Cloud</u></h2>

<p>open command prompt goto your app directory and and enter this<br><br>
<code><i>npm install @ionic/cloud --save</i></code><br>

Then Copy 'ionic.cloud.min.js' file to www/lib with this command</br><br>
<code><i>cp node_modules/@ionic/cloud/dist/bundle/ionic.cloud.min.js www/lib</i></code><br><br>

Then Install Bluebird<br><br>
<code><i>npm install bluebird --save</i></code><br><br>

Copy bluebird.min.js to www/lib<br><br>
<code><i>cp node_modules/bluebird/js/browser/bluebird.min.js www/lib</i></code><br><br>

Change index.html to like this<br>
<pre><&lt;script src="lib/bluebird.min.js"&gt;&lt/script&gt;
&lt;script src="lib/ionic/js/ionic.bundle.js"&gt;/script&gt;
&lt;script src="lib/ionic.cloud.min.js"&gt;&lt/script&gt;
</pre>

Connect ionic app to ionic cloud
<pre>ionic io init</pre>
This will automatically create an app in the Dashboard of app.ionic.io, and set the generated App ID in your io.config.json file.
<pre>If you’ve previously created an app on the Dashboard and you want to sync a local project, set the app ID in the io.config.json file.</pre>

<b>inject 'ionic.cloud' to app module</b>

</p>
<h4>Push Notification Setup</h4>

<b><i>make sure your cordova version is >= 6.4.0</i></b><br><br>

inspall push plugin
<pre>cordova plugin add phonegap-plugin-push --variable SENDER_ID=12341234 --save
</pre>
<i>you will get SENDER_ID from <a href="https://console.firebase.google.com/" target="_blank">FCM</a></i><br>
<pre>Go to FCM-->Login-->create New App-->click on app -->Click on settings Icon on top left-->click project settings-->click Cloud Messaging Tab

You will get SENDER_ID from there</pre>
<i>For more details <a href="https://docs.ionic.io/services/profiles/#android-fcm-project--server-key" target="_blank">click here</a></i>

Add the followin lines in app.js before <b>.run</b> function

<pre>.config(function($ionicCloudProvider) {
  $ionicCloudProvider.init({
    "core": {
      "app_id": "APP_ID"
    },
    "push": {
      "sender_id": "SENDER_ID",
      "pluginConfig": {
        "ios": {
          "badge": true,
          "sound": true
        },
        "android": {
          "iconColor": "#343434"
        }
      }
    }
  });
})
</pre>

You can find App Id from .io.config.json and snder id from <a href="https://console.firebase.google.com/" target="_blank">FCM</a><br>
To get push notification register device to ionic cloud.Put this code inside <b>.run</b> function in app.js
<pre>
if (window.cordova.plugins) {
      cordova.plugins.Keyboard.hideKeyboardAccessoryBar(true);
      cordova.plugins.Keyboard.disableScroll(true);

       $ionicPush.register().then(function(t) {
         return $ionicPush.saveToken(t,{ignore_user:true});
          }).then(function(t) {
        console.log('Token saved:', t.token);
        });

    }
   </pre>

To handle pushnotification when application is open
<pre>
$rootScope.$on('cloud:push:notification', function(event, data) {
  var msg = data.message;
  alert(msg.title + ': ' + msg.text);
});
</pre>

<i>Install cocoapodes for newer version of Xcode</i>
<pre>
for X-code 8
install cocoapods

sudo gem install cocoapods

git clone https://github.com/CocoaPods/Specs.git ~/.cocoapods/repos/master(this will take long time)
then set up cocoapads by command

http://stackoverflow.com/questions/30812777/cannot-install-cocoa-pods-after-uninstalling-results-in-error

pod setup
</pre>

<h2><u>Connect Ionic Cloud app to APN server</u></h2>

(for more details <a href="https://docs.ionic.io/services/profiles/#ios-push-certificate" target="_blank">Click here</a>)<br>

<h4><u>Create App</u></h4>

Goto <a href="https://developer.apple.com/account/" target="_blank"> Apple Developer Account</a> and signin then go to dassboard.<br><br>
Navigate to Certificates,Id's & Profiles › Identifiers -->App IDs in the Apple Developer Center and create an App id by clicking <b>+</b> button.<br>
Enter App name and Bundle Id as per config.xml<br>

<h4><u>Register Device</u></h4>
For developing mode push you need to register ur device to get push<br>

goto Device-->All --> Click on '+' button.Enter Device name and UUId.(to get UUID connect device to MAC.Open iTunes-->Click on Phone button.will get UUID from there)<br>

<h4><u>Create Keychain On MAC</u></h4>
Next Step is create Keychain on Mac..Open Finder In Mac-->goto Application-->Utilities-->Open Keychain..Click on Keychain Access menu on top left of MAC-->Certificate Assistant --› Request a Certificate From a Certificate Authority<br>..Enter your name and email address And CA name. Leave the CA Email blank.Click on Save to Disk radio button and hit continue. This will generate your <b>'.certSigningRequest'</b> file.Create a folder with name CERTIFICATES or something like that and save this file to that folder.

<h4><u>Create iOS App Certificate & Provisioning Profile</u></h4>

Again Come back to 'https://developer.apple.com/account/'..Click On Certificates-->Development-->Click on '+' button to add new certificate-->Click On 'iOS App Development' radio button --> click Continue-->continue the step and select '.certSigningRequest' that we saved on CERTIFICATES folder and click continue..Download certificate and open it.It will open in keychain Access Window under login tab..If it is not open Drag it to Keychain Access window under login tab.Right Click On that certificate on Keychain Access window Click on export save it as '.p12'. formate in CERTIFICATES folder..it will ask password.so provide a password and hit ok..goto CERTIFICATES folder and rename as IOS_CERT.p12

<h4><u>Create Provisioning Profile</u></h4>
Again Come back to 'https://developer.apple.com/account/'..Click on provisionig profile-->Development-->click On '+' button-->click on 
iOS App Development radio button and hit continue-->Select ur APP ID as per config.xml from drop down ht continue -->Select certificates hit continue-->select devices and continue-->enter profile name -->Download It and save it to CERTIFICATES folder.

<h4><u>Create APN Certificate</u></h4>
Again Come back to 'https://developer.apple.com/account/'..Click on APP Ids -->click on your app -->Click edit -->Enable push notification -->click on create certificates in the development SSL section-->'.certSigningRequest' that we saved on CERTIFICATES folder and click continue..Download certificate and open it.It will open in keychain Access Window under login tab..If it is not open Drag it to Keychain Access window under login tab.Right Click On that certificate on Keychain Access window Click on export save it as '.p12' formate in CERTIFICATES folder..it will ask password.so provide a password and hit ok..goto CERTIFICATES folder and rename the file as APN.p12.<br>

Now we have created all certificates for development push<br>
<h4><u>Add Certificates to Ionic Cloud</u></h4>

Goto <a href="https://apps.ionic.io/apps" target="_blank">Ionic Cloude</a> and open your App.Click on settings -->certificates->New Securiti profile-->enter name and hit Create Button.wait for sometimes Profile will show on the screen..Click on edit.A popup will shown..Select APN.p12 in 'APN Certificate' section from CERTIFICATES folder and enter its Password. Select IOS_CERT.p12 in 'App Development/App Store Certificate' section from CERTIFICATES folder and enter its Password.select '.mobileprovision' file from CERTIFICATES folder in the section 'Provisioning Profile' and hit Save.

<h4><u>Send Push</u></h4>

Click On Push tab continue step and select security profile and send Push...

<h4><u>Send Push By Postman</u></h4>

url:--  <b>https://api.ionic.io/push/notifications</b><br><br>

header:-->  <b>Authorization   :   Bearer FCM SERVER KEY</b><br>

You can find FCM SERVER KEY from <a href="https://console.firebase.google.com/" target="_blank">FCM</a> -->Your App<br>

POSTMAN Body

<pre>
{
    "tokens": ["evEXkkOI7nk:APA91bHgn4NJlj3xleX8h4Om3hiTQS2vCq9jmsvNdN8RZ_cXbztT_RJLv-fMKlagQ0W_HMCRmxqOy1joj7eIroVUxXsFGTVvdHQlXMouL7JzAWtsLtJqsJN9Mp3v2Bi9GI2-fm9DA5DF"],
    "profile": "push",
    "notification": {
        "message": "This is my demo push!"
    }
}
</pre>

Token will get from console when App start and register to ionic cloude.Profile is your security profile on ionic.io
</html>



