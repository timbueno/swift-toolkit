# Readium Swift Toolkit

[Readium Mobile](https://github.com/readium/mobile) is a toolkit for ebooks, audiobooks and comics written in Swift & Kotlin.

> [!TIP]
> **Take a look at the [guide to quickly get started](docs/Guides/Getting%20Started.md).** A [Test App](TestApp) demonstrates how to integrate the Readium Swift toolkit in your own reading app.

## Leaf position-cue experiment

This experimental branch, `leaf/3.11.1-position-cue`, adds the read-only `EPUBNavigatorViewController.currentReflowLocation` property on top of `leaf/3.11.1` at `1118fb62a`. It exposes the same validated text locator used internally to reload spreads, so the app can decorate the preserved passage without independently capturing one. The property returns nil until a matching anchor is available. This API is not part of the upstream reflow PR; The branch also publishes the restored anchor when restoration leaves the scroll offset unchanged, preventing a stale chapter-start anchor after spread reloads. Regression coverage checks same-page restoration and spread round trips. Revert to `1118fb62a` to remove this experiment.

## Leaf maintenance branch

This is Leaf's temporary Readium fork, maintained on `leaf/3.11.1`. It is based on official [Readium 3.11.1](https://github.com/readium/swift-toolkit/releases/tag/3.11.1) (`9a8c094b72245fd27043c9d298005e5c78b32a01`). It does not incorporate the unrelated changes on this fork's `develop` branch.

### Deviations from official 3.11.1

- **EPUB passage preservation during reflow** (`092d118d7`): retain a visible text anchor inside Readium when a paginated, horizontal left-to-right EPUB changes layout. Restore the anchor during resize processing and use it when rebuilding spreads, reducing position drift and delayed corrections. No app-supplied anchor or new public API is required. Includes regenerated JavaScript bundles.
- **Viewport resize ordering** (`0c73d1492`): also restore on the window resize event, since the body resize callback may run before the viewport width updates. Snap with the current width rather than a cached width. The mixed Chinese/English repeated-resize regression passes at both tested font sizes.
- **Regression coverage**: a self-contained navigator test exercises repeated font changes, spread reloads, and adopting a new passage after navigation. It passes on iPad and Duo runtimes and fails on unmodified 3.11.1. It was executed through Leaf's test harness; this does not establish a full Readium test-suite pass or visual fold verification.
- **This README section** documents Leaf's maintenance policy; it is excluded from the upstream contribution.

The corresponding upstream candidate is maintained separately on [`fix/text-anchor-reflow-develop`](https://github.com/timbueno/swift-toolkit/tree/fix/text-anchor-reflow-develop), based on official `develop`. It has not yet been submitted or accepted upstream. Relevant patch corrections and tests should be carried between these branches without merging unrelated development changes into Leaf's branch.

Leaf should pin an exact tested commit from this branch. Once an official Readium release includes the accepted fix, migrate Leaf to that release and retire this maintenance branch. Update this deviation list whenever a patch is added, changed, or removed.

## Features

✅ Implemented &nbsp;&nbsp;&nbsp;&nbsp; 🚧 Partially implemented  &nbsp;&nbsp;&nbsp;&nbsp; 📆 Planned &nbsp;&nbsp;&nbsp;&nbsp; 👀 Want to do &nbsp;&nbsp;&nbsp;&nbsp; ❌ Not planned

### Formats

#### Ebook and Document Formats

| Format                                                 | Status |
|--------------------------------------------------------|:------:|
| EPUB (reflowable)                                      |   ✅   |
| EPUB (fixed-layout)                                    |   ✅   |
| PDF                                                    |   ✅   |
| Readium Web Publication                                |   🚧   |
| [DAISY](https://daisy.org/activities/standards/daisy/) |   👀   |

#### Audiobook Formats

| Format                                                 | Status |
|--------------------------------------------------------|:------:|
| Readium Audiobook                                      |   ✅   |
| Zipped Audiobook                                       |   ✅   |
| Standalone audio files (MP3, AAC, etc.)                |   ✅   |
| [DAISY](https://daisy.org/activities/standards/daisy/) |   👀   |

#### Comic Formats

| Format               | Status |
|----------------------|:------:|
| Readium Divina       |   ✅   |
| CBZ (Comic Book ZIP) |   ✅   |
| CBR (Comic Book RAR) |   ❌   |

### Features

A number of features are implemented only for some publication formats.

| Feature                       | EPUB (reflow) | EPUB (FXL) | PDF |
|-------------------------------|:-------------:|:----------:|:---:|
| Pagination                    |       ✅      |     ✅     |  ✅ |
| Scrolling                     |       ✅      |     👀     |  ✅ |
| Right-to-left (RTL)           |       ✅      |     ✅     |  ✅ |
| Search in textual content     |       ✅      |     ✅     |  ✅ |
| Highlighting (Decoration API) |       ✅      |     ✅     |  👀 |
| Text-to-speech (TTS)          |       ✅      |     ✅     |  👀 |
| Media overlays                |       📆      |     📆     |     |

### OPDS Support

| Feature                                                                                           | Status |
|---------------------------------------------------------------------------------------------------|:------:|
| [OPDS Catalog 1.2](https://specs.opds.io/opds-1.2)                                                |   ✅   |
| [OPDS Catalog 2.0](https://specs.opds.io/opds-2.0)                                               |   ✅   |
| [Authentication for OPDS](https://drafts.opds.io/authentication-for-opds-1.0.html)                |   📆   |
| [OPDS Progression](https://github.com/opds-community/drafts/pull/91)                              |   📆   |
| [Readium LCP Automatic Key Retrieval](https://readium.org/lcp-specs/notes/lcp-key-retrieval.html) |   📆   |

### DRM Support

| Feature                                                                   | Status |
|---------------------------------------------------------------------------|:------:|
| [Readium LCP](https://www.edrlab.org/projects/readium-lcp/)               |   ✅   |
| [Adobe ACS](https://www.adobe.com/fr/solutions/ebook/content-server.html) |   ❌   |


## User Guides

Guides are available to help you make the most of the toolkit.

### Publication

* [Opening a publication](docs/Guides/Open%20Publication.md) – parse a publication package (EPUB, PDF, etc.) or manifest (RWPM) into Readium `Publication` models
* [Extracting the content of a publication](docs/Guides/Content.md) – API to extract the text content of a publication for searching or indexing it
* [Searching a publication](docs/Guides/Search.md) – search the textual content of a publication and navigate to matching results
* [Text-to-speech](docs/Guides/TTS.md) – read aloud the content of a textual publication using speech synthesis
* [Accessibility](docs/Guides/Accessibility.md) – inspect accessibility metadata and present it to users


### Navigator

* [Navigator](docs/Guides/Navigator/Navigator.md) - an overview of the Navigator to render a `Publication`'s content to the user
* [Configuring the Navigator](docs/Guides/Navigator/Preferences.md) – setup and render Navigator user preferences (font size, colors, etc.)
* [Integrating the Navigator with SwiftUI](docs/Guides/Navigator/SwiftUI.md) – glue to setup the Navigator in a SwiftUI application
* [Implementing Highlights](docs/Guides/Navigator/Highlights.md) – add and manage highlights in a publication
* [Font families in the EPUB navigator](docs/Guides/Navigator/EPUB%20Fonts.md) – support custom font families with reflowable EPUB publications
* [EPUB image preview](docs/Guides/Navigator/EPUB%20Image%20Preview.md) – detect tapped images and present an image detail view

### DRM

* [Supporting Readium LCP](docs/Guides/Readium%20LCP.md) – open and render LCP DRM protected publications

## Setting up the Readium Swift toolkit

### Minimum Requirements

<!-- https://swiftversion.net/ -->

| Readium | iOS | Swift compiler | Xcode |
|---------|-----|----------------|-------|
| `develop` | 15.0 | 6.0 | 16.4  |
| 3.8.0 | 15.0 | 6.0 | 16.4 |
| 3.0.0 | 13.4 | 5.10 | 15.4 |
| 2.5.1 | 11.0 | 5.6.1 | 13.4 |
| 2.5.0 | 10.0 | 5.6.1 | 13.4 |
| 2.4.0 | 10.0 | 5.3.2 | 12.4 |


### Dependencies

The toolkit's libraries are distributed with [Swift Package Manager](#swift-package-manager) (recommended) and [CocoaPods](#cocoapods). It's also possible to clone the repository (or a fork) and [depend on the libraries locally](#local-git-clone).

The [Test App](TestApp) contains examples on how to use all these dependency managers.

#### Swift Package Manager

From Xcode, open **File** > **Add Packages** and use Readium's GitHub repository for the package URL: `https://github.com/readium/swift-toolkit.git`.

You are then free to add one or more Readium libraries to your application. They are designed to work independently.

If you're stuck, find more information at [developer.apple.com](https://developer.apple.com/documentation/swift_packages/adding_package_dependencies_to_your_app).

#### CocoaPods

Add the following `pod` statements to your `Podfile` for the Readium libraries you want to use:

```
source 'https://github.com/readium/podspecs'
source 'https://cdn.cocoapods.org/'

pod 'ReadiumShared', '~> 3.11.1'
pod 'ReadiumStreamer', '~> 3.11.1'
pod 'ReadiumNavigator', '~> 3.11.1'
pod 'ReadiumOPDS', '~> 3.11.1'
pod 'ReadiumLCP', '~> 3.11.1'
```

Take a look at [CocoaPods's documentation](https://guides.cocoapods.org/using/using-cocoapods.html) for more information.

#### Local Git Clone

You may prefer to use a local Git clone if you want to contribute to Readium, or if you are using your own fork.

First, add the repository as a Git submodule of your app repository, then checkout the desired branch or tag:

```sh
git submodule add https://github.com/readium/swift-toolkit.git
```

Next, drag and drop the whole `swift-toolkit` folder into your project to import Readium as a Swift Package.

Finally, add the Readium libraries you want to use to your app target from the **General** tab, section **Frameworks, Libraries, and Embedded Content**.

### Building with Readium LCP

Using the toolkit with Readium LCP requires additional dependencies, including the framework `R2LCPClient.framework` provided by EDRLab. [Contact EDRLab](mailto:contact@edrlab.org) to request your private `R2LCPClient.framework` and the setup instructions.

## Credits

This project is tested with BrowserStack.
