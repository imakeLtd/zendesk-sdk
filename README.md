# nativescript-zendesk-sdk
[![npm](https://img.shields.io/npm/v/nativescript-zendesk-sdk.svg)](https://www.npmjs.com/package/nativescript-localize)
[![npm](https://img.shields.io/npm/dt/nativescript-zendesk-sdk.svg?label=npm%20downloads)](https://www.npmjs.com/package/nativescript-localize)

A NativeScript plugin implementing the basic Zendesk SDK in TypeScript.
It is inspired from [nativescript-zendesk](https://github.com/sitefinitysteve/nativescript-zendesk)

## Install
```shell
tns plugin add nativescript-zendesk-sdk
```

## Usage

Following [Zendesk Embeddables Documentation](https://developer.zendesk.com/embeddables):

### [Must do] Configure an app in Zendesk Support
_Support SDK for [Android](https://developer.zendesk.com/embeddables/docs/android/configure_an_app) / [iOS](https://developer.zendesk.com/embeddables/docs/ios/configure_an_app)_

### [Must do] Initialize the SDK
_Support SDK for [Android](https://developer.zendesk.com/embeddables/docs/android/initialize_sdk) / [iOS](https://developer.zendesk.com/embeddables/docs/ios/initialize_sdk)_
```typescript
import { ZendeskSdk } from "nativescript-zendesk-sdk";
...
ZendeskSdk.initialize("zendeskUrl", "applicationId", "clientId");
```
### [Must do] Set an identity (authentication)
_Support SDK for [Android](https://developer.zendesk.com/embeddables/docs/android/set_an_identity) / [iOS](https://developer.zendesk.com/embeddables/docs/ios/set_an_identity)_

#### Authenticate using an anonymous identity
```typescript
ZendeskSdk.setAnonymousIdentity(null, null);
```

#### Authenticate using an anonymous identity (with COPPA compliance)
```typescript
ZendeskSdk.setCoppaEnabled(true);

ZendeskSdk.setAnonymousIdentity(null, null);
```

#### Authenticate using an anonymous identity (with details)
```typescript
ZendeskSdk.setAnonymousIdentity("name", "email");
```

#### Authenticate using your JWT endpoint
```typescript
ZendeskSdk.setJwtIdentity("jwtUserIdentifier");
```

### Show the Help Center
_Support SDK for [Android](https://developer.zendesk.com/embeddables/docs/android/show_help_center) / [iOS](https://developer.zendesk.com/embeddables/docs/ios/show_help_center)_

#### Default Usage
```typescript
ZendeskSdk.showHelpCenter();
```

#### Optional Parameters
```typescript
const options = {
    showConversationsMenuButtonForAndroid: boolean = true,
    showConversationsMenuButtonForIos: boolean = true,
    withCategoriesCollapsedForAndroid: boolean = false,
    showContactUsButtonForAndroid: boolean = false,
    showAsModalForIos: boolean = false,
}

ZendeskSdk.showHelpCenter(options);
```

_Note:  The SDKs for Android and iOS diverge quite a bit for this section, so some options are only applicable to iOS or Android._

##### Both

###### showConversationMenuButtonFor(Android/Ios) [_default = true_]
Enables a button in the navigation bar that enables users to view their active requests/converations or start a new one.

##### Android only

###### showContactUsButtonForAndroid [_default = false_]
This displays an additional `(+)` button in the lower-right corner, similar to the button in the Android templates.

While it is enabled by default in the SDK, it has been disabled by default here since:
1. It is not available on iOS
2. It is not necessary, since it is also available in the conversations menu
3. It's usage is ambiguous to begin with and just clutters the screen. (It was likely only added since the SDK developer used the default Android template to start with and just left it in.)

###### withCategoriesCollapsedForAndroid [_default = false_]
This collapses the categories into their headers. The default behaviour on both Android and iOS is to show the first 5 of a category, and then has the option to expand further if more are available.

##### iOS only

###### showAsModalForIos [_default = false_]
This displays the helpcenter as a modal action sheet.

### Filter the Help Center
_Support SDK for [Android](https://developer.zendesk.com/embeddables/docs/android/filter_help_center) / [iOS](https://developer.zendesk.com/embeddables/docs/iOS/filter_help_center)_

Per original SDKs, only one filter can be used at a time.

#### Filter by category
```typescript
showHelpCenterForCategoryIds(categoryIds: number[], options);
```

#### Filter by section
```typescript
showHelpCenterForLabelNames(categoryIds: string[], options);
```

#### Filter by article label
```typescript
showHelpCenterForSectionIds(categoryIds: number[], options);
```

### Create a request
```typescript
createRequest(
    requestSubject?: string,
    additionalInfo?: string,
    addDeviceInfo: boolean = true,
    ...tags: string[]);
```

## Styling
_Support SDK for [Android](https://developer.zendesk.com/embeddables/docs/android/customize_the_look) / [iOS](https://developer.zendesk.com/embeddables/docs/ios/customize_the_look)_

### Android
Configured via `app/App_Resources/Android/AndroidManifest.xml` as detailed [here](https://developer.zendesk.com/embeddables/docs/android/customize_the_look).

### iOS
```typescript
import { isIOS } from 'tns-core-modules/platform';

...

if ( isIOS ) {
    UINavigationBar.appearance().tintColor = new Color('#00FFFF').ios;
    UINavigationBar.appearance().barTintColor = new Color('#FF00FF').ios;
    UINavigationBar.appearance().titleTextAttributes =
        <NSDictionary<string, any>>NSDictionary.dictionaryWithObjectForKey(
            new Color('#FFFF00').ios,
            NSForegroundColorAttributeName);
}

const iOSTheme: ZendeskIosThemeSimple = {
    primaryTextColor:          '#FF0000',
    secondaryTextColor:        '#00FF00',
    primaryBackgroundColor:    '#0000FF',
    secondaryBackgroundColor:  '#00FFFF',
    emptyBackgroundColor:      '#FF00FF',
    metaTextColor:             '#FFFF00',
    separatorColor:            '#884444',
    inputFieldTextColor:       '#448844',
    inputFieldBackgroundColor: '#444488',
    fontName:                  'Courier',
    boldFontName:              'Cochin-BoldItalic',
};
ZendeskSdk.setIosTheme(iOSTheme);
```
The first 3 colors are used primarily on the ActionBar/StatusBar for the "new ticket" screen, as the Help Center uses the default ActionBar/StatusBar colors from the regular NativeScript setup for those.

These settings could affect the whole app, but are ignored by the regular NativeScript Views, but could potentially impact other 3rd part views. Likely you will set these to be the same as what is used in the rest of the app.

The settings within the theme object will only affect the Zendesk.

## Contributions
Typings and iOS metadata have been included in the project to allow for easier usage.  
Typings were autogenerated using:  
https://github.com/NativeScript/android-dts-generator  
https://docs.nativescript.org/runtimes/ios/how-to/Use-Native-Libraries  
Although some manual changes had to be made by commenting-out types and setting to `any` that NativeScript handles the conversions for, such as NSArray and `java.util.List`.  
Current typings/metadata were generated using version `1.9.2.2` of the Zendesk and Zendesk Provider SDKs.

## Dumping typings...
##### iOS
```sh
pod repo update
TNS_TYPESCRIPT_DECLARATIONS_PATH="$(pwd)/typings" npm run demo.ios
cp typings/x86_64/objc\!Zendesk* typings/
```

##### Android
```sh
cd android-sdk
./gradlew clean
./gradlew getDeps

cd lib
jar xf sdk-1.9.2.1.aar
mv classes.jar used-zendesk-sdk.jar
jar xf sdk-providers-1.9.2.1.aar
mv classes.jar used-zendesk-providers-sdk.jar

rm -rf */
find . -type f ! -iname "zendesk-*" -delete
cd ../..

java -jar ../android-dts-generator/dts-generator/build/libs/dts-generator.jar -input \
    android-sdk/lib/used-zendesk-sdk.jar \
    && mv out/android.d.ts typings/java\!ZendeskSDK.d.ts;
java -jar ../android-dts-generator/dts-generator/build/libs/dts-generator.jar -input \
    android-sdk/lib/used-zendesk-providers-sdk.jar \
    && mv out/android.d.ts typings/java\!ZendeskProviderSDK.d.ts;
```
