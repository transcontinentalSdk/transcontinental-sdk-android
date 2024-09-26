# Changelog

## [2.1.1] - 2024-09-19

### Changed

- Updated Compose BOM to `2024.05.00`
- Updated other dependencies in response to Compose BOM updates

### Fixed

- Fixed several search bar/search text state inconsistencies
- Fixed in-block video/audio mismatch on stopping video

## [2.1.0] - 2024-08-16

The v2.1.0 release introduces several new features and improvements to the SDK, bringing it closer to parity with the TC Web SDK:

- Configurable in-block and partial-block video playback (muted by default)
- Carousel views with manual or automatic ("slide-show") navigation
- Ability to perform internal (deep) or external (web) linking functionality as provisioned by the TC API

### Fixed

- Fixed outbound date encoding when using `listPublication` API
- Fixed environment URLs being incorrectly decoded in certain release types

## [2.0.1] - 2024-05-02

### Fixed

- Fixed a crash on some large static guidebooks by using screen-appropriate sizing

## [2.0.0] - 2024-03-21

The v2.0.0 update introduces substantial performance improvements for the SDK, driven by several optimizations and technical enhancements:

- Reduced time to first image when opening a flyer with the new rendering engine by re-writing manifest parsing
- Reduced total memory usage by 5-10x by eagerly caching to disk and evicting off-screen images from memory
- Improved scrolling responsiveness by moving most/all download/parsing/rendering-related actions to worker threads. This results in less "janky" scrolling when viewing very large flyers
- Reduced bandwidth requirements by enabling HTTP Range Requests to allow partial downloads, instead of re-downloads
- Reduced likelihood of temporarily blank screens by dynamically changing download/rendering priorities to prefer what should be currently on-screen, and deferring all off-screen images

These updates will facilitate some upcoming performance enhancements, namely pre-fetching. Pre-fetching will allow the SDK to optionally download and cache images before they are needed, resulting in a much smoother user experience and an even lower time to first image.

These improvements are the result of a significant re-write of the SDK's rendering engine, and as such, we are releasing this as a breaking version change.

## [1.1.6] - 2022-10-11

### Changed

- Orientation can be applied to any device (no longer overridden by screen size)

### Fixed

- Internal usage of screen size is taken at each usage and not cached (enabling correct usage of the flyer on secondary displays)
- Prevented logo cropping in horizontal mode

## [1.1.5] - 2022-09-29

### Fixed

- Fixed missing logo by adding SVG logo support
- Fixed image artifacts caused by incorrect formatting on some images

## [1.1.1-20220713] - 2022-07-13

- Cherry pick of 1.1.4 fixes

## [1.1.4] - 2022-07-12

### Fixed

- Network reachability should return correct connection status when accessed through a VPN

## [1.1.3] - 2022-06-03

### Added

- The `LandingData` object now contains a friendly `displayTitle` string

### Fixed

- Internal product SKUs should not be emitted by the SDK

## [1.1.2] - 2022-05-17

### Changed

- The TCSdkModel object accepts "fr" in lieu of "bil" for French language stores/flyers.

## [1.1.1] - 2022-04-01

### Parameter Initialization

The `TcSdkModel` is now a singleton object with an initialization function named `setSdkParams`:

#### Previously:

```kotlin
val tcSdkModel = TCSdkModel()
tcSdkModel.storeId = "myStoreId"
tcSdkModel.language = "en" // "en" or "bil"
tcSdkModel.banner = "myBanner"
tcSdkModel.environment = "staging" // "staging", "uat", "live"
tcSdkModel.client = "myClient"
tcSdkModel.date = "2021/12/31” // Optional
```

#### Now:

```kotlin
TCSdkModel.setSdkParams(
    "myBanner",
    "myClient",
    "staging", // "staging", "uat", "live"
    "myStoreId",
    "en", // "en" or "bil"
    Date() // Optional - defaults to current date
)
```

#### Store/Language Modification

The following methods have been made available on the `TCSdkModel` singleton in order to modify the `store id` and/or `language`:

```kotlin
TCSdkModel.setStoreId("myStoreId")
TCSdkModel.setLanguage("en")
TCSdkModel.setStoreIdAndLanguage("myStoreId", "en")
```

### `ERR_NO_BASE_URL_FOUND` replaced by `ERR_PARAM_VALIDATION`

If any parameters required by the Web API are missing, a `ErrorStatus.ERR_PARAM_VALIDATION` will be returned to the caller.

### Removed Viewgroup

There is no longer the need to pass the `viewGroup` to the `gotoFlyerDetailPage` as it is now created within the SDK.

#### Previously

```kotlin
TCDigitalFlyer(context).gotoFlyerDetailPage(
    viewGroup,
    productListener,
    tcSdkUIComponents,
    landingResponse
)
```

#### Now

```kotlin
TCDigitalFlyer(context).gotoFlyerDetailPage(
    productListener,
    tcSdkUIComponents,
    landingData
)
```