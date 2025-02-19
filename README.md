# Overview

This repo demonstrates that `ShareExtension` target still exists in the output of `npx expo config` after installation.

The [README.md](https://github.com/achorein/expo-share-intent?tab=readme-ov-file#ios-extension-target) specifies:

> When building on EAS you should only have one extension target (during credentials setting process).
>
> To avoid expo auto configuration to add an experimental "appExtensions" to app.json you must manually configure your eas build (projectId in > app.json and a eas.json file).

And links to [this issue](https://github.com/achorein/expo-share-intent-demo/issues/1).

However, despite following the instructions above, `npx expo config` outputs the following:

```typescript
 extra: {
    router: {
      origin: false
    },
    eas: {
      projectId: 'd1273804-db22-4f23-8059-447d75a05275',
      build: {
        experimental: {
          ios: {
            appExtensions: [
              {
                targetName: 'ShareExtension',
                bundleIdentifier: 'com.vaharoni.shareintenttest.share-extension',
                entitlements: {
                  'com.apple.security.application-groups': [
                    'group.com.vaharoni.shareintenttest'
                  ]
                }
              }
            ]
          }
        }
      }
    }
  }
```

And when running `eas build --local --platform ios --profile development`, the output deemed problematic by the [linked issue](https://github.com/achorein/expo-share-intent-demo/issues/1) is observed:

```
Failed to read the app config from the project using "npx expo config" command: Unexpected token 'e', "[expo-share"... is not valid JSON.
Falling back to the version of "@expo/config" shipped with the EAS CLI.
✔ Using remote iOS credentials (Expo server)

We found that the scheme you want to build consists of many targets.
You have to set up credentials for each of the targets.
They can share the same Distribution Certificate but require separate Provisioning Profiles.

Setting up credentials for following targets:
- Target: shareintenttest
  Bundle Identifier: com.vaharoni.shareintenttest
- Target: ShareExtension
  Bundle Identifier: com.vaharoni.shareintenttest.share-extension

Setting up credentials for target shareintenttest (com.vaharoni.shareintenttest)
```

# Repo Reproduction

This repo was created by running:

```bash
npx create-expo-app@latest
npx expo install expo-dev-client
eas build
npm install expo-share-intent patch-package
```

The following was added to `package.json`:

```json
"scripts": {
   "postinstall": "patch-package"
}
```

A patch file `xcode+3.0.1.patch` was added to `patches/` with [this content](https://github.com/achorein/expo-share-intent/blob/main/example/basic/patches/xcode%2B3.0.1.patch).

The following was added to `app.json`:

```json
"plugins": [
   "expo-share-intent"
]
```

Note that `scheme` was left intact in `app.json` (but it exists).
