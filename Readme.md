# React Native guide

React Native guide with useful hints/articles/libraries.

*All information here is about environment/useful libraries/react native pitfalls, so it perfectly fits for developers, who knows JavaScript/React, but didn't work with React Native or did it long time ago.*

# Getting started

[Official React Native guide](https://reactnative.dev/docs/environment-setup)

```bash
react-native init *projectname*
react-native run-ios
react-native run-android
```

## Navigation

[Wix navigation (react-native-navigation)](https://wix.github.io/react-native-navigation/docs/installing)

```bash
yarn add react-native-navigation
react-native link react-native-navigation
```

After install `react-native run-android` will throw an error `Cannot get property 'supportLibVersion' on extra properties extension as it does not exist`.

To fix this, add to `android/build.gradle`:

```java
buildscript {
    ...
}

subprojects { subproject ->
    afterEvaluate {
        if ((subproject.plugins.hasPlugin('android') || subproject.plugins.hasPlugin('android-library'))) {
            android {
                variantFilter { variant ->
                    def names = variant.flavors*.name
                    // To check for a certain build type, use variant.buildType.name == "<buildType>"
                    if (names.contains("reactNative51") || names.contains("reactNative55") || names.contains("reactNative56") || names.contains("reactNative57") || names.contains("reactNative57_5") || names.contains("reactNative60")) {
                        // Gradle ignores any variants that satisfy the conditions above.
                        setIgnore(true)
                    }
                }
            }
        }
    }
}

allprojects {
    ...
}
```

On the moment of writing this, the last react native version was 0.62. Maybe you need to add more versions to `if` statement (e.g. `names.contains("reactNative62")`).

## Absolute components import

[Babel-plugin-module-resolver](https://github.com/tleunen/babel-plugin-module-resolver) allows you to import components absolute from root folder (usually it's `src` folder).

```
yarn add --dev babel-plugin-module-resolver
```

Add to `babel.config.js`:

```js
module.exports = {
  ...
  plugins: [
    ['module-resolver', {
      'root': ['./src'], //or your root folder
    }],
  ],
};
```

For WebStorm/IntelliJ autocompletion just mark your `src` folder as resources root (right click in IDE on folder -> Mark as directory as -> Resource Root). Guides for other IDE's are in library documentation.

# Libraries

## Using svg images

[React-native-svg](https://github.com/react-native-community/react-native-svg) allows you to use svg images as components or as `<Image />` tag source.

**Must-have library for quality images in application**

```bash
yarn add react-native-svg
cd ios && pod install
```

To use svg files as separated components, you need to install one more library:

```bash
yarn add --dev react-native-svg-transformer
```

After install, update `metro.config.js`:

```js
const { getDefaultConfig } = require('metro-config');

module.exports = (async () => {
  const {
    resolver: { sourceExts, assetExts },
  } = await getDefaultConfig();
  return {
    transformer: {
      babelTransformerPath: require.resolve('react-native-svg-transformer'),
      getTransformOptions: async () => ({
        transform: {
          experimentalImportSupport: false,
          inlineRequires: false,
        },
      }),
    },
    resolver: {
      assetExts: assetExts.filter(ext => ext !== 'svg'),
      sourceExts: [...sourceExts, 'svg'],
    },
  };
})();
```

Then you can use .svg as follows:

```
import SVGImage from 'image.svg';

const Icon = () => (
    <SVGImage width={100} height={100} />
);
```

## Local storage

[@react-native-community/async-storage](https://github.com/react-native-community/async-storage) allows you to store application data locally. It looks like [localStorage](https://developer.mozilla.org/ru/docs/Web/API/Window/localStorage) in web, but it is asynchronous and has more widely API.

```bash
yarn add @react-native-community/async-storage
cd ios/ && pod install
```

## Native modals

[react-native-modal](https://github.com/react-native-community/react-native-modal)

```bash
yarn add react-native-modal
```

Example:
```js
import React, { useState } from 'react';
import { Button, Text, View } from 'react-native';
import Modal from 'react-native-modal';

const ModalTester = () => {
  const [isModalOpen, setIsModalOpen] = useState(false);

  const toggleModal = () => setIsModalOpen(!isModalOpen);

  return (
      <View style={{ flex: 1 }}>
        <Button title="Show modal" onPress={toggleModal} />
        <Modal isVisible={isModalVisible}>
          <View style={{ flex: 1 }}>
            <Text>Hello!</Text>
            <Button title="Hide modal" onPress={toggleModal} />
          </View>
        </Modal>
      </View>
    );
}
```

## Native range (slider) component

[@react-native-community/slider](https://github.com/react-native-community/react-native-slider)

```bash
yarn add @react-native-community/slider
cd ios && pod install
```

Example:
```js
import React, { useState } from 'react';
import Slider from '@react-native-community/slider';

const SliderComponent = () => {
  return (
     <Slider
       style={{width: 200, height: 40}}
       minimumValue={0}
       maximumValue={1}
      />
    );
}
```

## Device info

[react-native-device-info](https://github.com/react-native-community/react-native-device-info) allows you to get many useful info about use device

```bash
yarn add react-native-device-info
cd ios && pod install
```

## Push notifications

[@react-native-firebase/messaging](https://rnfirebase.io/messaging/usage)

For usage you need to install firebase/app module and generate `google-services` files, full instructions are by the link above. Integration process is complex, you should carefully go through all steps. Be sure you didn't skip any of them.

## "Rate application" button

[react-native-rate](https://github.com/KjellConnelly/react-native-rate)

Need to set your google bundle id (for google play link) & apple app id from developer console (for apple store link).

```bash
yarn add react-native-rate
cd ios && pod install
```

## Update application version

[Very handy tool](https://github.com/stovmascript/react-native-version) to change version in both (ios/android) platforms. Install this package globally, update `version` field in `package.json` and be happy.

```bash
yarn global add react-native-version
cd /yourProject
react-native-version
```

## Text to speech

[react-native-tts](https://github.com/ak1394/react-native-tts)

```bash
yarn add react-native-tts
react-native link react-native-tts
cd ios && pod install
```

Example:
```js
Tts.speak('Hello, world!');
```

## Facebook login button

[react-native-fbsdk](https://github.com/facebook/react-native-fbsdk#installation)

```bash
yarn add react-native-fbsdk
cd ios && pod install
```

## VK login button

[react-native-fbsdk](https://github.com/doomsower/react-native-vkontakte-login#installation)

```bash
yarn add react-native-vkontakte-login
cd ios && pod install
```

# Articles

## Change bundle id

For iOS: Open XCode project, General tab, field "bundle identifier"
For Android:
- Rename the project' subfolder from: "android/app/src/main/java/MY/APP/OLD_ID/" to: "android/app/src/main/java/MY/APP/NEW_ID/"
- android/app/src/main/java/MY/APP/NEW_ID/AnyActivityFile.java: `package MY.APP.NEW_ID;`
- Rename the project' subfolder from: "android/app/src/debug/java/MY/APP/OLD_ID/" to: "android/app/src/debug/java/MY/APP/NEW_ID/"
- android/app/src/debug/java/MY/APP/NEW_ID/ReactNativeFlipper.java: `package MY.APP.NEW_ID;`
- android/app/src/main/AndroidManifest.xml: `package="MY.APP.NEW_ID"`
- android/app/build.gradle: `applicationId "MY.APP.NEW_ID"`
- android/app/BUCK:
```
android_build_config(
  package="MY.APP.NEW_ID"
)
android_resource(
  package="MY.APP.NEW_ID"
)
```
- Gradle' cleaning in the end (in /android folder): `./gradlew clean`


## App icons

[Good guide](https://medium.com/better-programming/react-native-add-app-icons-and-launch-screens-onto-ios-and-android-apps-3bfbc20b7d4c) with all steps for both platforms (including launch screen, but I prefer method in guide below)

## Splash screen

[Good guide](https://medium.com/@appstud/add-a-splash-screen-to-a-react-native-app-810492e773f9) about how to add splash screen to your application on both platforms

## Publish app to Google Play

[Official guide](https://reactnative.dev/docs/signed-apk-android) for android with steps to generate release bundle. Bundle will be created in folder `android/app/build/outputs/bundle/release`.

\* Development build: 
```bash
cd android
./gradlew assembleRelease
```

Build will be created in folder `android/app/build/outputs/apk/release`.

## Publish app to App Store

[Step-by-step guide](https://readybytes.in/blog/how-to-deploy-a-react-native-ios-app-on-the-app-store) for ios. In my opinion, there are too many information, so i'll try to write it shortly:
1. Create development/production certificates and provisioning profiles in Apple developer account. That are steps 3-5 from guide above.
2. Add your developer account to Xcode (*projectname*->Signing & Capabilities tab->Team), then check Automatically manage signing. Your provision profile should be choosen automatically
3. Set correct build target. In upper part of XCode window, near "Build" and "Stop" icon buttons, you can choose build target. Choose your project and set "Generic iOS Device"
4. Build app. Xcode menu -> Product -> Build (or cmd+B hotkey)
5. Archive app. Xcode menu -> Product -> Archive
6. Distribute app. After archivation, you will see a window with all of your archived builds. You should press "distribute" button and choose "Upload to App Store" checkbox. Then click "next" and let Xcode make all automatically. On last step click "upload".
7. \* Development distribution. You can add iPhone devices for testing your app by uuid in Apple Developer Account. To make build for them, choose "Development" instead of "Upload to App Store" after pressing "distribute" button, and on last step click "export". Then you can upload .ipa file on [diawi](https://diawi.com/) and share with your testing team.