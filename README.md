# OCTUS Aadhar offline ANDROID SDK
![version](https://img.shields.io/badge/version-v0.1.0-blue)

The Octus aadhar offlines SDK comes with a two screens one is for enter aadhar number and captcha and second one is for verifying otp.
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

***NOTE : Encryption of OCTUS AADHAR OFFLINE SDK Result is under development***

You will need a valid license to use the Octus aadhar offline SDK, which can be obtained by contacting `support@frslabs.com` . 

Depending on the license - offline or online - you have opted for, the ping functionality to billing servers will be disabled or enabled. For instance, if you have opted for the offline SDK model, then there will be no server ping needed to our billing server to bill you. However, if you have chosen a transaction based pricing, then after each transaction, a ping request will be made to our billing server. This cannot be overrided by the App. A point to note is that if the ping transaction fails for any reason, the whole transaction will be void without any results from the SDK.

Once you have the license , follow the below instructions for a successful integration of Octus aadhar offline SDK onto your Android Application.

## Android SDK Requirements

**Minimum SDK Version** -  **17** or higher

## Download

#### Using maven repository

Add the following code to your `project` level `build.gradle` file

```groovy
allprojects { 
    repositories { 

        maven { 
            // URL for Octus aadhar offline SDK. 
            url "https://octus-aadhar-offline-android.repo.frslabs.space/"                  
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
    /* Dependencies for Octus aadhar offline SDK */ 
    implementation 'com.android.support:design:<version above 23.4.0>'      
    implementation 'com.android.support.constraint:constraint-layout:<version above 1.1.3>'
   
    // octus aadhar offline Core Dependency
    implementation 'com.frslabs.android.sdk:octus_aadahr_offline:0.1.0'
    
    // OPTIONAL - Required if transaction based billing is enabled
    // octus aadhar offline billing dependencies
    implementation 'com.frslabs.android.sdk:torus:0.1.0'
    implementation 'com.google.code.gson:gson:2.8.5'
   
}
```

## Setup

#### Permissions

OctusAadharOffline requires the following permission to operate properly

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="your.package.name" >

    <!-- Optional - Required if transaction based billing is enabled -->
    <uses-permission android:name="android.permission.INTERNET" />  
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    
    <application>
      ...
    </application>

</manifest>
```
## Quick Start

#### Initiating the Octus aadhar offline Sdk

Initialize the `Octus aadhar offline` instance with the appropriate configurations to invoke the Octus aadhar offline Sdk


```java
public class MainActivity extends AppCompatActivity {

    // ...

    /* Enter the Vidus license key here */
    private String LICENCE_KEY_OCTUS_AADHAR_OFFLINE_SDK = "ENTER_YOUR_LICENSE_KEY_HERE";
    
    /* (OPTIONAL)  Enter the OctusAadharOffline api credentials here */
    private String OCTUS_AADHAR_OFFLINE_API_BASE_URL = "ENTER_BASE_URL_HERE"
            , OCTUS_AADHAR_OFFLINE_API_CRED1 = "ENTER_API_CRED1_HERE"
            , OCTUS_AADHAR_OFFLINE_API_CRED2 = "ENTER_API_CRED2_HERE";
   
    
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
                .setApiCredentials(new OctusAadharOfflineApiCredentials(OCTUS_AADHAR_OFFLINE_API_BASE_URL
                        , OCTUS_AADHAR_OFFLINE_API_CRED1
                        , OCTUS_AADHAR_OFFLINE_API_CRED2))
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

Following error codes will be returned on the `onVidusFailure` method of the callback

| CODE | DESCRIPTION                  |
| ---- | ---------------------------- |
| 803  | Permission denied             |
| 804  | SDK was interrupted       |
| 805  | OctusAadharOffline SDK License expired            |
| 806  | OctusAadharOffline SDK License was invalid |
| 807  | Invalid Config         |
| 808  | Transaction Failed       |
| 809  | No Internet Available             |
| 811  | Network Error |


## Help
For any queries/feedback , contact us at `support@frslabs.com` 
