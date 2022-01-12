<p align="center">
  <a href="https://trackbar.com" >
    <img src="logo_light.svg#gh-dark-mode-only" width="150" />
    <img src="logo_dark.svg#gh-light-mode-only" width="150" />
  </a>
</p>


-----------

<img alt="GitHub release (latest by date)" src="https://img.shields.io/github/v/release/trackbar-fitness/trackbarsdk?label=release">

Trackbar is a sensor that is attached to weight lifting equipment. Measuring critical parameters like speed and range of motion it provides the coach and the athlete with insights about the workout and the progress. Further, it offers communication tools to intensify the relationship between athlete and coach. For both parties trackbar offers the possibility to digitize the workout.

We are measuring e.g. the range of motion, speed and eccentric / concentric movement times. In addition to inertial measurement units (IMU) we fuse the data with the latest pressure sensors which makes our measurements even more precise.

Download
-----------

Include [jitpack.io](jitpack.io "jitpack.io") as remote repository in your root build.gradle, for gradle version 7.x.x in your settings.gradle
```
dependencyResolutionManagement {
  repositories {
    maven { url 'https://jitpack.io' }
  }
}
```

Include SDK In your app build.gradle
```
android {
  ...
  aaptOptions {
    noCompress "tflite"
    }
}
dependencies {
  ...
  implementation 'com.github.trackbar-fitness:trackbarsdk:LATEST_VERSION'
}
```

You can check out releases from GitHub [releases page][1].


Implementation
------

Create instance of TrackbarManager class:
```
val instance = TrackbarManager.instance(applicationContext, partnerId, token)
```

`partnerId` and `token` can be retrieved after subscribing to Trackbar.
If these parameters are not specified or incorrect, none of the functions below will work

**Permissions**

Developer does not need to request bluetooth permissions since it is handled by SDK

**Listeners**

SDK contains three types of listeners: TrackbarListner, TrainingListener, ErrorListener

To register listeners, call:
 
```
instance.addListener(TrackbarListener)
instance.addTrainingListener(TrainingListener)
instance.addErrorListener(ErrorListener)
```
To remove listeners, call: 
```
instance.removeListener(TrackbarListener)
instance.removeTrainingListener(TrainingListener)
instance.removeErrorListener(ErrorListener)
```
**Trackbar State**

`instance.connectionState` returns one of SDK-to-trackbar status:  *Connection.NONE, Connection.CONNECTED, Connection.CONNECTING, Connection.DISCONNECTING, Connection.DISCONNECTED*

`instance.isScanning` indicates if SDK is currently in scanning mode

`instance.isWorkingOut` indicates if SDK is processing a workout, this field is set to *true* after *instance.stopTraining()* is called

`instance.isProcessingWorkout` indicates if SDK algorithm is processing workout data

`instance.isUploadingWorkout` indicates when SDK uploads processed workout to AWS

`instance.isReconnecting` indicates when SDK tries to reconnect previous connected device


**Scanning for trackbar device**

In order to connect to trackbar, first we need to scan/find the device by calling
```
instance.startScanning()
or
instance.startScanning(scanPeriodSeconds: Long, scanMode: Int)
```
`scanPeriodSeconds` tells SDK for how much time should stay scanning, defaiult 0 infinitely

`scanMode` (default ScanSettings.SCAN_MODE_LOW_LATENCY) can be one of:
```
ScanSettings.SCAN_MODE_BALANCED
ScanSettings.SCAN_MODE_LOW_LATENCY
ScanSettings.SCAN_MODE_LOW_POWER
ScanSettings.SCAN_MODE_OPPORTUNISTIC
```
After a scan is performed, TrackbarListener will emit: 
```
onScanStatus(Scan.SCANNING)
onDeviceStatus(@DeviceStatus status: Int, device: Device?, message: String)
```
If bluetooth permission is not granted during scanning, ErrorListener will emit:
```
onError(Error.PERMISSION_NOT_GRANTED)
```

**Connecting to trackbar**

To connect to a trackbar device, call:
```
instance.connect(address:String)
```
if bluetooth is not enabled, ErrorListener will emit:
```
onError(Error.BLUETOOTH_NOT_ENABLED)
```

If a connection is lost, it can be reconnected by calling:
```
instance.reconnect()
```
To disconnect from trackbar, call:
```
instance.disconnect()
```

**Training**

In order to start training algorithm, call:
```
val info = InitialInformation(
  athleteName:String,
  coachName:String,
  workoutName:String,
  weightText:String,
  movementCategorization:Int
)
instance.start(info)
```
TrainingListener will emit:
```
onTrainingStart(name: String)
```
or, if any error occurs while starting, ErrorListener will emit any of: 
```
onError(Error.WORKOUT_PROCESSING)
onError(Error.BLUETOOTH_NOT_ENABLED)
```
to stop training, call:
```
instance.stop()
```
SDK will stop training, process data and upload them to AWS
During these steps, TrainingListener will emit:
```
onTrainingComplete(name: String, success: Boolean, error:Exception?, stats: List<StatData>)
onUploadStart(fileName: String)
onUploadProgress(fileName: String, progress: Int)
onUploadComplete(fileName: String, success: Boolean, error: Throwable?)
```

**Log In**

User can log in  to our database via SDK by calling:
```
instance.logIn(email:String, password:String, callback:(LoginResult))
```


Compatibility
---------

Minimum Android SDK: 21

Compiled Android SDK: 31


Author
--------
SKAIN GmbH


License
-------
License Agreement
for the limited use of the
Trackbar Software Development Kit Version 
Version 1.0, 18th December 2021
1. Agreement to the Terms and Conditions 
This License Agreement (ÒLicenseÓ) is a legal agreement between you (ãLicenseeÒ or ãyouÒ) and Skain GmbH (Gesellschaft mit beschrŠnkter Haftung), …sterreich (ãSkainÒ). In order to use the first, not fully tested release of the Trackabr Software Development Kit (ãSDKÒ or ãTrackbar SDKÒ), you must agree to the terms of this License. Only if you agree to the terms of this License, you are entitled to use the SDK according to the terms of this License. Otherwise you may not use the SDK. 
2. License subject 
(a) Subject to the terms and conditions of this License , Skain hereby grants you a worldwide, non-exclusive, limited, no-charge, royalty-free license to use the SDK for the exclusive purpose to test and evaluate the SDK in combination with your mobile application. The License is limited by third party components contained in the SDK that are subject to separate and differing license terms attached as Appendix to this License. The Licensee shall be fully liable for complying with such third party license terms and shall defend, indemnify and hold Skain harmless against any claims arising from the LicenseeÕs use of such third party components. 
(b) This License grants you the right to use the SDK to make your mobile applications for non-commercial use exclusively, which may incorporate the SDK in whole or in part in binary or object code, and to test these mobile applications with the Apple TestFlight as well as Android PlayStore service platform. This License explicitly does not grant you the right to publish or test mobile applications that contain the SDK or parts of it with the Apple App Store or Android PlayStore. 
3. Redistribution and Sublicensing 
(a) For the purpose under (2a) this License grants you the right to distribute the runtime portion of the Trackbar SDK, on a royalty-free basis, solely as embedded or incorporated into your mobile application and solely to third parties to whom you license your mobile application pursuant to an agreement that is no less protective of Skain as this License. 
(b) For the purpose under (2a) you may sublicense the rights granted under (2b) solely to third parties to whom you license your mobile application to act as distributors thereof pursuant to an agreement no less protective of Skain as this License. 
4. Contract Term 
This License is limited to the term of one year after delivery of the SDK from Skain to you. At the end of the term all rights granted to you by means of this License Agreement expire. Moreover, either Skain or Licensee may terminate the License Agreement at any time subject to giving 7 days prior written notice. 
5. Restrictions and Limitations 
(a) Skain reserves all rights not expressly granted to Licensee. You may not use the SDK for any purpose not expressly permitted by this License. 
(b) You agree that as a condition of this License you will design and distribute your mobile application to ensure that your mobile application and any software or hardware required to use your mobile application does not, and you will not, alter or interfere with the normal operation, behavior or functionality of the Trackbar SDK, including Skain software security features. 
(c) You may not (except as and only to the extent any following restriction is prohibited by applicable law): (a) decompile; (b) reverse engineer; (c) disassemble; (d) attempt to derive the source code of the SDK or any part of the SDK, or any other software or firmware provided by Skain. 
(d) You also agree not to commit any act intended to interfere with the normal operation of the Trackbar SDK or other Skain products, or provide software to users or developers that would induce breach of any law or agreement you entered into with Skain or that contains malware, viruses, hacks, bots, trojan horses, other malicious code, pornography or any other inappropriate content or functionality that might harm SkainÕs reputation. 
6. Confidentiality 
(a) Confidential Information means any and all information of Skain that is not generally known by the public. This includes with respect to Skain, but is not limited to: (a) information related to products, technical data, methods, processes, know-how and inventions, (b) information related to development, research, testing, marketing and financial activities and strategic plans, (c) information related to the manner in which Skain operates, (d) information related to costs and sources of supply, (e) information related to the identity and special needs of customers, employees and prospective customers, and (f) information related to persons and entities with whom Skain has business relationships and the nature and substance of those relationships. Confidential Information also includes any information that Skain may receive or has received from customers, subcontractors, suppliers or others, with any understanding, express or implied, that the information would not be disclosed. 
(b) Confidential Information shall not include anything that Licensee can document (a) was generally available to the public at the time it was received by Licensee, or (b) was known to Licensee, without restriction, at the time of disclosure. 
(c) It may be necessary and desirable that Skain discloses Confidential Information to the Licensee, however, on a strict need-to-know basis only. 
(d) Licensee acknowledges that the Confidential Information constitutes or contains trade secrets of Skain respective its licensors and remains property of Skain respective its licensors. 
(e) Licensee shall keep the Confidential Information strictly confidential and shall not disclose it to third parties (including its subsidiaries, parent or affiliated companies) without prior written permission of Skain. 
(f) Confidential Information received hereunder shall not be used for any purpose other than the purpose permitted explicitly in this License Agreement. 
(g) Licensee shall restrict access to Confidential Information to only those of its employees to whom such access is necessary for carrying out the permitted use and advise such employees of the obligations assumed in this License Agreement. 
(h) Neither Licensee, nor Licensee's employees shall sell, transfer, publicly disclose, display or otherwise make available to third parties any portion of the SDK or any other Confidential Information. Licensee agrees to secure and protect Confidential Information by all appropriate technical and non-technical means, and to take all appropriate actions with its employees who are permitted access thereto, to keep Confidential Information confidential. 
7. Privacy 
(a) You acknowledge and accept SkainÕs privacy policy, which is provided by Skain at https://trackbar.com/datenschutz. You agree that the Trackbar SDK may send data to Skain to check for updates, provide aggregated usage statistics of your use of the SDK and the use of your mobile application by end users and provide optional Developer Services. 
(b) You acknowledge and agree that Skain may deliver messages and contact you about the SDK and other product and service offerings. 
(c) You agree to distribute your mobile application with a privacy policy explaining the data you collect through your mobile application and how you collect, use, share, and protect it; and to include a disclosure that Skain is your service provider and collects certain data from your mobile application, along with a link to Skain's privacy policy (https://trackbar.com/datenschutz), which may be updated from time to time. 
(d) Under this License, Skain does not collect any personal data from users of the mobile application of Licensee. Licensee agrees to not let Skain get any of their knowledge about personal data from users of the mobile application of Licensee. 
8. Limitation of Liability 
(a) The Trackbar SDK is provided ãas isÒ, without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose and noninfringement. 
(b) Skain shall be liable, without limitation in amount, for damages arising from wilful or grossly negligent breach of duty by Skain or one of its legal representatives or vicarious agents. Furthermore, Skain shall be liable, without limitation in amount, for damages arising from death, injury to body or health that result from wilful or grossly negligent breach of duty by Skain or one of its legal representatives or vicarious agents. 
(c) Skain shall otherwise be liable for slight negligence only if an obligation is breached the observance of which is of particular importance for achieving the purpose of the contract (cardinal obligation/substantial contractual obligation), and limited to such damages which were typically foreseeable at the time the contract was concluded. Liability for damages shall only apply to damages resulting directly from a breach of the contractual conditions. 
9. Miscellaneous 
(a) Legal ineffectiveness of individual provisions shall not affect the remaining parts of this agreement. The parties agree that ineffective provisions shall be replaced by provisions that come as close as possible to the economic intent and the purpose of the agreement. 
(b) No failure or delay in enforcing any right under this License Agreement will be deemed to constitute a waiver of such right. 
(c) This License shall be governed and construed in accordance with the laws of Austria and any dispute arising out of or in connection therewith shall be subject to the exclusive jurisdiction of the Courts of Vienna. 
Appendix 
The SDK was created using Software from Apple Inc. (X-Code, Swift, iOS-SDKs and other) and from Google LLC (Android Studio, Kotlin, android-SDK and other). The terms of the Apple Developer Program apply accordingly: https://developer.apple.com/terms/ , for the Android Program apply accordingly: https://developer.android.com/studio/terms. 
The following tools and software packages are not included in the iOS-Trackbar SDK. Their terms apply as far as you use them in combination with our SDK in your mobile application: 
* CocoaPod (MIT): https://github.com/CocoaPods/CocoaPods/blob/master/LICENSE 
* Tensorflow (Apache-2.0): https://github.com/tensorflow/tensorflow/blob/master/LICENSE
The following tools and software packages are not included in the android-Trackbar SDK. Their terms apply as far as you use them in combination with our SDK in your mobile application: 
* Tensorflow (Apache-2.0): https://github.com/tensorflow/tensorflow/blob/master/LICENSE


[1]: https://github.com/trackbar-fitness/trackbarsdk/releases
