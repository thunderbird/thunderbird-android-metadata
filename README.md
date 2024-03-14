# Thunderbird Android Metadata

This repository contains F-Droid metadata for the K-9 Mail and Thunderbird for Android app.

## Information

We follow the [Fastlane Supply](https://docs.fastlane.tools/actions/supply/) file structure for our metadata: [All About Descriptions, Graphics, and Screenshots](https://f-droid.org/en/docs/All_About_Descriptions_Graphics_and_Screenshots/#in-the-apps-source-repository).

Please also have a look at Google Play for [Graphic assets, screenshots, & video](https://support.google.com/googleplay/android-developer/answer/9866151?hl=en&visit_id=638460088895328299-2981363794&rd=1).

## Structure

Every app's metadata is placed in a directory named after the `applicationId`. Within this folder, the metadata follows the Fastlane [supply](https://docs.fastlane.tools/actions/supply/) structure, see example below:

```
├── en-US                       (en-US is the F-Droid fallback language)
│   ├── short_description.txt   (short description, max 80 chars, mandatory)
│   ├── full_description.txt    (full app description, mandatory)
│   ├── title.txt               (app name)
│   ├── video.txt               (URL to a video introducing the app)
│   ├── images
│   │   ├── icon.png            (app icon, mandatory if your app doesn't include any png icon)
│   │   ├── featureGraphic.png  (promo banner, shown on top of the app desc in F-Droid client; landscape)
│   │   ├── tvBanner.png        ("icon" for TV devices, currently not used)
│   │   ├── phoneScreenshots
│   │   │   ├── 1.png
│   │   │   ├── 2.png
│   │   │   ...
│   │   ├── sevenInchScreenshots/
│   │   ├── tenInchScreenshots/ (you may add different screenshots for different screen sizes)
│   │   ├── tvScreenshots/
│   │   └── wearScreenshots/
│   └── changelogs
│       ├── 100000.txt          (must correspond to versionCode, literally, no padding)
│       ├── 100100.txt          (if the version code was set to 100100)
│       └── 100101.txt          (maximum size: 500 characters)
└── es                          (other locale codes)
    ...                         (localized metadata is always preferred by the client)
    └── changelogs
        └── 100100.txt
```

## How to

### Update metadata

To update the metadata, you can simply edit the files in the `applicationId` directory and push the changes to the repository.

### Integrate metadata into the app's source repository

There are [4 options](https://gitlab.com/fdroid/fdroidserver/-/blob/master/fdroidserver/update.py?ref_type=heads#L1031) to integrate the metadata into the app's source repository:

1. `/src/{buildFlavor}/fastlane/metadata/android/{locale}`
2. `/fastlane/metadata/android/{locale}`
3. `/metadata/{locale}`
4. `/metadata/{applicationId}/{locale}`

The metadata is stored in the `app-metadata` directory of the app's source repository. The `app-metadata` directory should be at the root of the repository.

Create a git submodule in the app's source repository:

```sh
git submodule add https://gitlab.com/fdroid/thunderbird-android-metadata.git app-metadata
```

To allow F-Droid to pick up the metadata while building the app, you need to link from `app-metadata/{applicationId}` to `metadata` to comply with the 3rd lookup location. Modify your `{applicationId}.yml` at `fdroiddata` to include the following:

```yaml
Builds:
  - versionName: "{versionName}"
    versionCode: { versionCode }
    ...
    prebuild: ln -s app-metadata/{applicationId} metadata
```

or if `subdir` is set:

```yaml
Builds:
  - versionName: "{versionName}"
    versionCode: { versionCode }
    ...
    subdir: {subdir}
    prebuild: ( cd .. && ln -s app-metadata/{applicationId} metadata )
```
