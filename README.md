How to publish Flutter app on Play Store: Beginner guide

How to publish Flutter app on Play Store and App Store?
1. Prepare app:
	a. App Signing with key generate.
	b. Android files configuration.
	c. Build App release for upload.
2. Set up a new app on Play Store
	a. Internal Testers configuration
	b. App content setup 
	c. Upload the appbundle
#happy Fluttering.

Aug 10, 2023
Read in 2 minutes
In this post we take a look at how to publish a flutter app on Play Store
Signing the app
To publish on the Play Store, you need to give your app a digital signature. Use the following instructions to sign your app.
Create an upload keystore
On Mac/Linux, use the following command:
keytool -genkey -v -keystore ~/upload-keystore.jks -keyalg RSA -keysize 2048 -validity 10000 -alias upload
On Windows, use the following command:
keytool -genkey -v -keystore %userprofile%\upload-keystore.jks -storetype JKS -keyalg RSA -keysize 2048 -validity 10000 -alias upload
This command stores the upload-keystore.jks file in your home directory.
Reference the keystore from the app
Create a file named [project]/android/key.properties that contains a reference to your keystore:
storePassword=<password from previous step>
keyPassword=<password from previous step>
keyAlias=upload
storeFile=<location of the key store file, such as /Users/<user name>/upload-keystore.jks
*Warning: Keep the key.properties file private; don’t check it into public source control.
Configure signing in gradle
Configure gradle to use your upload key when building your app in release mode by editing the [project]/android/app/build.gradle file.
•	Add the keystore information from your properties file before the android block:
``
def keystoreProperties = new Properties()
   def keystorePropertiesFile = rootProject.file('key.properties')
   if (keystorePropertiesFile.exists()) {
       keystoreProperties.load(new FileInputStream(keystorePropertiesFile))
   }
 
 android {
         ...
   }`
This loads the key.properties file into the keystoreProperties object.
•	Find the buildTypes block:
buildTypes {
       release {
           // TODO: Add your own signing config for the release build.
           // Signing with the debug keys for now,
           // so flutter run --release works.
           signingConfig signingConfigs.debug
       }
   }
And replace it with the following signing configuration info:
signingConfigs {
       release {
           keyAlias keystoreProperties['keyAlias']
           keyPassword keystoreProperties['keyPassword']
           storeFile keystoreProperties['storeFile'] ? file(keystoreProperties['storeFile']) : null
           storePassword keystoreProperties['storePassword']
       }
   }
   buildTypes {
       release {
           signingConfig signingConfigs.release
       }
   }
Final:flutter build appbundle

![image](https://github.com/SayedMainUddin/maindocfiles/assets/55692156/7f1f0de7-9459-44c9-b104-f4850d21e3e8)
