# react-native-qq [![npm version](https://badge.fury.io/js/react-native-qq.svg)](http://badge.fury.io/js/react-native -qq)

React Native's QQ login plugin, react-native version requires 0.40.0 and above

## how to install

### First install the npm package

```bash
Yarn add react-native-qq
```
or

```bash
Npm install react-native-qq --save
```

Then execute

```bash
React-native link react-native-qq
```

### Installing iOS Project

Add `libRCTQQAPI.a, libiconv.tbd, libsqlite3.tbd, libz.tbd, libc++.tbd to the `Build Phases->Link Binary with Libraries` of the project target.

In `Build Settings->Search Paths->Framework Search Paths` (if you can't find Framework Search Paths, please select All in the Build Settings, instead of Basic) and add the path `$(SRCROOT)/../node_modules /react-native-qq/ios/RCTQQAPI`

Add `-framework "TencentOpenAPI" to `Build Settings->Link->Other Linker Flags`.

Add `-isystem "$(SRCROOT)/../node_modules/react-native-qq/ios/RCTQQAPI" to `Apple LLVM X.X - Custom Compiler Flags->Link->Other C Flags`

Add qq whitelist to the project plist file: (required for ios9 or above)
Please open Info.plist as text and add it
```xml
<key>LSApplicationQueriesSchemes</key>
<array>
    <!-- QQ, Qzone URL Scheme Whitelist -->
    <string>mqqapi</string>
    <string>mqq</string>
    <string>mqqOpensdkSSoLogin</string>
    <string>mqqconnect</string>
    <string>mqqopensdkdataline</string>
    <string>mqqopensdkgrouptribeshare</string>
    <string>mqqopensdkfriend</string>
    <string>mqqopensdkapi</string>
    <string>mqqopensdkapiV2</string>
    <string>mqqopensdkapiV3</string>
    <string>mqzoneopensdk</string>
    <string>wtloginmqq</string>
    <string>wtloginmqq2</string>
    <string>mqqwpa</string>
    <string>mqzone</string>
    <string>mqzonev2</string>
    <string>mqzoneshare</string>
    <string>wtloginqzone</string>
    <string>mqzonewx</string>
    <string>mqzoneopensdkapiV2</string>
    <string>mqzoneopensdkapi19</string>
    <string>mqzoneopensdkapi</string>
    <string>mqzoneopensdk</string>
 </array>
```

Add the QQ scheme in `Info->URL Types`: `Identifier` is set to `qq`, `URL Schemes` is set to the APPID of your registered QQ developer account, prefixed with `tencent`, for example `tencent1104903684 `

Add the following code to your project's `AppDelegate.m` file:

```
#import <React/RCTLinkingManager.h>

- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation {
  Return [RCTLinkingManager application:application openURL:url sourceApplication:sourceApplication annotation:annotation];
}

```

### Installing Android Project

In `android/app/build.gradle`, add the following code under the defaultConfig column:

```
manifestPlaceholders = [
    QQ_APPID: "<APPID of Platform Application"
]
```

If you need to modify the APPID later, you only need to modify this one.

Also, make sure you have the implementation of `onActivityResult` in MainActivity.java:

```java
    @Override
    Public void onActivityResult(int requestCode, int resultCode, Intent data){
        super.onActivityResult(requestCode, resultCode, data);
        mReactInstanceManager.onActivityResult(requestCode, resultCode, data);
    }
```

## how to use

### Introducing the package

```
Import * as QQAPI from 'react-native-qq';
```

### API

#### QQAPI.login([scopes])

- scopes: Login permission to apply, the default is get_simple_userinfo. When multiple permissions are required, they are separated by commas.

Calling QQ login may jump to the QQ application or open a web browser for the user to log in. All subsequent login calls will fail directly before this login returns.

Returns a `Promise` object. The callback on success is an object like this:

```javascript
{
"access_token": "CAF0085A2AB8FDE7903C97F4792ECBC3",
"openid": "0E00BA738F6BB55731A5BBC59746E88D"
"expires_in": "1458208143094.6"
"oauth_consumer_key": "12345"
}
```

#### QQAPI.shareToQQ(data)

Share to QQ friends, parameters with QQAPI.shareToQzone, return a `Promise` object

#### QQAPI.shareToQzone(data)

Share to QZone, the parameter is an object, which can have the following form:

```javascript
// Share graphic messages
{
Type: 'news',
Title: share the title,
Description: description,
webpageUrl: web address,
imageUrl: remote image address,
}

// The rest of the format has not been implemented.
```

## common problem

#### Android: Calling QQAPI.login() does not respond

This reason usually occurs because Manifest is not configured, check the configuration of the Activity in Manifest.

#### Android: The QQ login has been successfully activated, but the callback has not been executed.

This reason usually occurs because the call to onActivityResult is missing from MainActivity.java.
