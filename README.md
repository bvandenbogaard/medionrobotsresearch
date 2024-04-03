# Medion Robots research notes

## About Medion Robots
Medion has several smart home appliances which can be controlled with apps such as Medion Life and Medion Robots.
The later was more of interest to me because I purchased a cheap robot vacuum cleaner, specifically the S10 SW (MD19601), which could only be controlled with the Medion Robots app.
This turned out to be a Tuya device, which is an IoT platform that controls many cheap white label IoT devices.
Most Tuya enabled devices advertise this on the package. But in this case, it seems that Medion decided not to do so.

## Home assistant and LocalTuya
Because I am a home automation enthousiast, I thought it would be fun to link this vacuum to my own local instance of Home assistant.
After some searching online I found very little technical information about my device. I did find the LocalTuya integration (https://github.com/rospogrigio/localtuya).

### Connecting the vacuum robot to LocalTuya
In order to communicate with the vacuum robot, you need to register the device with a local device key.
The LocalTuya readme tells you to use Cloud API or TinyTua. However both options seem possible with this device.
So the only way to retrieve those keys is by faking the Android app and extract the keys from there.
I set up a rooted Andriod environment to dump all HTTPS traffic the app was generating and tada, there it was!
After providing the localkey to LocalTuya, everything started working.

### Retrieving the localkey

First you have to login with your app account details by performing a POST request to https://production.medion.cloud/onboarding/v1/signin

```
{"user":"<REPLACE_WITH_YOUR_EMAILADDRESS>","password":"<REPLACE_WITH_YOUR_PASSWORD>","deviceId":"<REPLACE_WITH_A_MADE_UP_DEVICE_ID>","deviceName":"<REPLACE_WITH_A_MADE_UP_DEVICE_NAME>"}
```

deviceId has to be 16 random hexidecimal characters, suffixed with -ROBOTS, for example "0123456789abcdef-ROBOTS"

deviceName can be any string, for example "Raspberry Pi 4"

If done correctly, you will get a response with a refreshToken and an accessToken.

Use this accessToken as a Bearer token header value in the next request.

Do a GET request to https://production.medion.cloud/devices/v1/?applicationCategory=Robot and if done correctly, you should get a list of all devices linked to your account including the localKey!
