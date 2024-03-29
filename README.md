# Octus Aadhaar Offline SDK 
![version](https://img.shields.io/badge/version-v2.0.5-blue)

Aadhaar Paperless Offline eKYC is a secure and shareable document which can be used by any Aadhaar holder for offline verification of identification. The Aadhaar Offline document can be obtained from the UIDAI website. This SDK provides a simple plugin to your mobile App which allows the user to seamlessly share their offline Aadhaar file with the service provider. 


There are two ways the SDK can be configured within your App. The first one is an in-app experience whereby the pre-built screens will allow the Aadhaar holder to enter the Aadhaar Number or VID, Captcha, OTP and four digit share code all within your App without redirecting to the UIDAI website. The Aadhaar Offline file once downloaded will be parsed in-memory and displayed in the App (data shared with the App as JSON data). The experience is seamless with the in-app option.


The second option is to redirect the user to the UIDIAI website and allow the user to follow the instructions provided by the website. The user can enter the Aadhaar Number or VID, captcha, OTP and four digit share code in the website. Once the data is validated by UIDAI, a ZIP file (password protected using the share code) will be downloaded into the resident’s device. The user will now have to switch back to your App and select the file from the devices download folders which will parse the data and display them in the App (data shared with the App as JSON data). 


In both cases, the Aadhaar Offline file will be validated for its digital signature and the KYC data of The Aadhaar holder will be passed to the integrating App as JSON data.

You can find the release history at [Changelog](CHANGELOG.md)

# Table Of Content

- [Prerequisite](#prerequisite)
- [Android SDK Requirements](#android-sdk-requirements)
- [Download](#download)
  - [Using maven repository](#using-maven-repository)
- [Setup](#setup)
  - [Permissions](#permissions)
- [Quick Start](#quick-start)
- [OCTUS Aadhar Offline Result](#octus-aadhar-offline-result)
- [OCtus Aadhar offline Error Codes](#octus-aadhar-offline-error-codes)
- [Help](#help)

## Prerequisite


You will need a valid license to use the Octus Aadhaar Offline SDK, which can be obtained by contacting `support@frslabs.com` 

Depending on the license - offline or online - you have opted for, the ping functionality to billing servers will be disabled or enabled. For instance, if you have opted for the offline SDK model, then there will be no server ping needed to our billing server to bill you. However, if you have chosen a transaction based pricing, then after each transaction, a ping request will be made to our billing server. This cannot be overrided by the App. A point to note is that if the ping transaction fails for any reason, the whole transaction will be void without any results from the SDK.



## Android SDK Requirements

**Minimum SDK Version** -  **17** or higher

## Download

#### Using maven repository

Add the following code to your `project` level `build.gradle` file

```groovy
allprojects { 
    repositories { 

        maven { 
            // URL for Octus Aadhaar Offline SDK. 
            url "https://octus-android-aadhaar-offline.repo.frslabs.space/"                  
            credentials { 
                username '<YOUR_USERNAME>' 
                password '<YOUR_PASSOWRD>' 
            }
        }
       
       /*
        *Include below code only for transaction based billing
        */
        //Maven credentials for the Torus SDK
        maven {
            url "https://torus-android.repo.frslabs.space/"
            credentials {
                username '<YOUR_USERNAME>'
                password '<YOUR_PASSOWRD>'
            }
        }
        
    }
}
```

After that, add the following code to your `app` level `build.gradle` file

```groovy
// ...

defaultConfig { 

    // ...

    vectorDrawables.useSupportLibrary true 
    
}

// ...
```

And then, add the dependencies
```groovy

// ...

dependencies {

    /* Dependencies for Octus Aadhaar Offline SDK */ 
    implementation 'androidx.legacy:legacy-support-v4:<lastest verison>'
    
    implementation 'androidx.appcompat:appcompat:<lastest verison>'
    
    implementation 'com.google.android.material:material:<lastest verison>'

    // octus aadhar offline billing dependencies
    implementation('com.frslabs.android.sdk:torus:1.0.0')
    implementation 'com.google.code.gson:gson:2.8.6'
   
    // octus aadhar offline Core Dependency
    implementation 'com.frslabs.android.sdk.octus:aadhaaroffline:2.0.5'
    
    // OPTIONAL - Required if transaction based billing is enabled
    //Zip
    implementation 'net.lingala.zip4j:zip4j:1.3.2'
   
}
```

## Setup

#### Permissions

Octus Aadhaar Offline SDK requires the following permission to operate properly

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="your.package.name" >

    <!-- Optional - Required if transaction based billing is enabled -->
    <uses-permission android:name="android.permission.INTERNET" />  
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <!-- Optional - Required if set node is 1(AADHAR OFFLINE REDIRECT) -->
     <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
  
    
    <application>
      ...
    </application>

</manifest>
```
## Quick Start

#### Initiating the Octus Aadhaar Offline SDK

Initialize the `Octus Aadhaar Offline SDK` instance with the appropriate configurations to invoke the Octus Aadhaar Offline SDK


```java
public class MainActivity extends AppCompatActivity {

    // ...

    /* Enter the Octus Aadhaar Offline license key here */
    private String LICENCE_KEY_OCTUS_AADHAR_OFFLINE_SDK = "ENTER_YOUR_LICENSE_KEY_HERE";
    
    /* (OPTIONAL)  Enter the OctusAadharOffline api credentials here */
    private String OCTUS_AADHAR_OFFLINE_API_BASE_URL = "ENTER_BASE_URL_HERE"
            , OCTUS_AADHAR_OFFLINE_KEY_ID = "ENTER_API_KEY_ID_HERE"
            , OCTUS_AADHAR_OFFLINE_KEY_SEC = "ENTER_API_KEY_SEC_HERE";
   
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button callSdk = findViewById(R.id.invoke_sdk);
        callSdk.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                /* Invoke the Octus Aadhar offline Sdk */
                callOctusAadharOfflineSdk();
            }
        });
    }

    public void callOctusAadharOfflineSdk() {

        OctusAadhaarOfflineConfig octusAadhaarOfflineConfig = new OctusAadhaarOfflineConfig.Builder()
                .setLicenseKey(LICENCE_KEY_OCTUS_AADHAR_OFFLINE_SDK)
                 /*  node value should be 0 or 1.
                 0 is for Aadhaar Offline in app ,
                 1 is for Aadhaar Offline redirect*/
                .setNode(node)
                /* If the user need to change button background color and text color need to add below lines  */
                .setButtonColorCode("#000000")
                .setTextColorCode("#FFFFFF")
                .setApiCredentials(new OctusAadharOfflineApiCredentials(OCTUS_AADHAR_OFFLINE_API_BASE_URL
                        , OCTUS_AADHAR_OFFLINE_KEY_ID
                        , OCTUS_AADHAR_OFFLINE_KEY_SEC))
                .build();

        OctusAadhaarOffline octusAadharOffline = new OctusAadhaarOffline(octusAadhaarOfflineConfig);
        octusAadharOffline.start(this, new OctusAadharOfflineResultCallback() {
            @Override
            public void onOctusOfflineSuccess(OctusAadharOfflineResult octusAadharOfflineResult) {
                /* Handle the OctusAadharOffline Sdk result here */
          Log.d(TAG, "onOctusOfflineSuccess:"+octusAadharOfflineResult.getOcrData());

            }

            @Override
            public void onOctusOfflineFailure(int errorCode) {
                /* Handle the onOctusOffline Sdk failure result here */
        Log.d(TAG, "onOctusOffline Failute: "+errorCode);
            }
        });

    // ...

}
```

#### Handling the result

Your activity must implement `OctusAadharOfflineResultCallback` to receive the result.

```java
    // ...
    @Override
    public void onOctusOfflineSuccess(OctusAadharOfflineResult octusAadharOfflineResult) {
                /* Handle the OctusAadharOffline Sdk result here */
          Log.d(TAG, "onOctusOfflineSuccess:"+octusAadharOfflineResult.getOcrData());

            }
    @Override
     public void onOctusOfflineFailure(int errorCode) {
                /* Handle the onOctusOffline Sdk failure result here */
        Log.d(TAG, "onOctusOffline Failute: "+errorCode);
            }

    // ...
```

## OctusAadharOfflineResult Result
The result is obtained through the `OctusAadharOfflineResult` object 

##### OctusAadharOfflineResult

<div>
<table style="width:100%">
 <tr>
 <th bgcolor="#F1F1F1" colspan="3">Public Methods</th>
 </tr>
 <tr>
 <td>String</td>
 <td>getOcrData()</td>
 <td>Gets the data of aadhar xml file</td>
 </tr>
</table>
</div>
## OctusAadharOffline Error Codes

Following error codes will be returned on the `onOctusOfflineFailure` method of the callback

| CODE | DESCRIPTION                  |
| ---- | ---------------------------- |
| 803  | Permission denied             |
| 804  | SDK was interrupted       |
| 805  | Octus Aadhaar Offline SDK License expired            |
| 806  | Octus Aadhaar Offline SDK License was invalid |
| 807  | Invalid Config         |
| 808  | Transaction Failed       |
| 809  | No Internet Available             |
| 811  | Network Error 
| 404  | UIDAI Website server down |
| 812  | User Interupted            |
| 813  | File Upload Failed            |
| 814  | Otp Limit Exceeded            |



#### In-App SDK configuration. 

This part of the integration allows you to integrate the drop-in screens for the in-app experience whereby the Aadhaar holder will enter the Aadhaar Number or VID, Captcha, OTP and four digit share code all within your App without redirecting to the UIDAI website. The Aadhaar Offline file once downloaded will be parsed in-memory and displayed in the App (data shared with the App as JSON data). The experience is seamless with the in-app option. However, one of the caveats to this configuration is that any change to the UIDIA website will need to be updated and is a reactive release and there is a risk of your App not working until the changes reflect in the SDK.

<div>
<table style="width:100%">
  <tr>
    <th bgcolor="#F1F1F1" colspan="2">Public Methods</th>
  </tr>
  <tr>
    <td><b>setNode</b>(<em>0</em>)</td>
    <td>For in-app configuration, set the node value to <b> 0 </b> </td>
  </tr>
</table>
</div>


#### Redirect to UIDAI website SDK configuration. 

This part of the integration will allow the user to upload the file if already downloaded and stored in the user’s device or redirect the user to the UIDIAI website and allow the user to follow the instructions to download the file. The user can enter the Aadhaar Number or VID, captcha, OTP and four digit share code in the website. Once the data is validated by UIDAI, a ZIP file (password protected using the share code) will be downloaded into the resident’s device. The user will now have to switch back to your App and select the file from the devices download folders which will parse the data and display them in the App (data shared with the App as JSON data). As this is the preferred way by UIDAI, the risk of failing even with the changes is negated to a large extent.

<div>
<table style="width:100%">
  <tr>
    <th bgcolor="#F1F1F1" colspan="2">Public Methods</th>
  </tr>
  <tr>
    <td><b>setNode</b>(<em>1</em>)</td>
    <td> For redirect to UIDAI configuration, set the node value to <b> 1 </b> </td>
  </tr>
</table>
</div>

## Help
For any queries/feedback, contact us at `support@frslabs.com` 
