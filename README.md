# Multi-Device Website Screenshots CI

Capture screenshots of any website — desktop **and** dozens of emulated mobile
devices — right inside your GitHub Actions workflow. Save them as build
artifacts, post them straight into a pull-request comment, or send them to a
Telegram chat.

[![Release](https://img.shields.io/github/v/release/Primajin/screenshots-ci-action?sort=semver&label=release)](https://github.com/Primajin/screenshots-ci-action/releases)
[![Runtime](https://img.shields.io/badge/runtime-node24-brightgreen?logo=node.js&logoColor=white)](action.yml)
[![puppeteer](https://img.shields.io/badge/puppeteer-v24-40b5a4?logo=puppeteer)](https://pptr.dev/)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

> **A maintained, modernized fork** of
> [`flameddd/screenshots-ci-action`](https://github.com/flameddd/screenshots-ci-action)
> (last released 2022). If you're weighing the two, here's what this fork brings:
>
> - 🐛 **`fullPage: false` actually works.** The original bundles puppeteer 14,
>   which ignored `fullPage: false` for emulated mobile devices and captured the
>   whole scrollable page anyway (a single iPad Pro shot came out ~25 000 px
>   tall). This fork runs **puppeteer v24**, so viewport screenshots behave as
>   documented.
> - 🟢 **Current runtime.** Runs on the **Node 24** action runtime (latest LTS);
>   the original still targets the end-of-life `node16`.
> - 📱 **Up-to-date device list** via puppeteer's `KnownDevices`.
> - 🚀 **Actively released** with
>   [release-please](https://github.com/googleapis/release-please) — pin a stable
>   major with `Primajin/screenshots-ci-action@v3`.
>
> Same inputs and outputs as the original, so it's a drop-in replacement:
> just swap `flameddd/screenshots-ci-action@master` for
> `Primajin/screenshots-ci-action@v3`.

## Parameters
| Name(type) | required(default) | Description |
| ------------- | ------------- | ------------- |
| `url`(string) | **required**(`""`) | The target website's URL to generate screenshots |
| `devices`(string) | optional(`""`) | Specific mobile devices to generate screenshots. **Use comma(`,`) to separate devices name.** Device name list in below. |
| `noDesktop`(boolean) | optional(`false`) | Set `true` if not require to get desktop viewpoint screenshots. |
| `fullPage`(boolean) | optional(`false`) | Set `true`, takes a screenshot of the full scrollable page. |
| `noCommitHashFileName`(boolean) | optional(`false`) | Set `true`, the filename would not have commit hash. |
| `type`(string) | optional(`jpeg`) | Specify screenshot type, can be either `jpeg` or `png`. |
| `waitUntil`(string) | optional(`networkidle0`) | When to consider navigation succeeded. <br> - `load`: consider navigation to be finished when the load event is fired.<br> - `domcontentloaded`: consider navigation to be finished when the DOMContentLoaded event is fired.<br> - `networkidle0`: consider navigation to be finished when there are no more than 0 network connections for at least `500` ms.<br> - `networkidle2`: consider navigation to be finished when there are no more than 2 network connections for at least `500` ms. <br><br> Consider set to `networkidle2` when target website has **long polling request**.|
| `waitForSelector`(string) | optional(`''`) | Wait for the **selector** to appear in page. If the **selector** doesn't appear after the `waitForSelectorTimeout` of waiting, it will take screenshots immediately. <br> See [README.waitForSelector](/README.waitForSelector.md) for example |
| `waitForSelectorTimeout`(number) | optional(`30000`) | **WaitForSelector Options:** <br> Maximum time to wait in milliseconds. Pass `0` to disable timeout. <br> - `30000` means 30 seconds |
| `waitForSelectorVisible`(boolean) | optional(`false`) | **WaitForSelector Options:** <br> Wait for the selected element to be present in DOM and to be visible, i.e. to not have `display: none` or `visibility: hidden` CSS properties. |
| `waitForSelectorHidden`(boolean) | optional(`false`) | **WaitForSelector Options:** <br> Wait for the selected element to not be found in the DOM or to be hidden, i.e. have `display: none` or `visibility: hidden` CSS properties. |
| `releaseId`(string) | optional(`"`) | Github Release Id, it's required to upload screenshots to PR comment. see [README.PR](/README.PR.md) for more detail |

## Envs
| Name(type) | required(default) | Description |
| ------------- | ------------- | ------------- |
| `TELE_CHAT_ID`(string) | optional(`""`) | Integration with **Telegram**. `screenshots-ci-action` will send screenshots to telegram. see [README.Telegram](/README.Telegram.md) for setting detail. |
| `TELE_BOT_TOKEN`(string) | optional(`""`) | Integration with **Telegram**. `screenshots-ci-action` will send screenshots to telegram. see [README.Telegram](/README.Telegram.md) for setting detail. |
| `GITHUB_TOKEN`(string) | optional(`""`) | Github Actions Tokens, it's required to upload screenshots to PR comment. See [README.PR](/README.PR.md) for more detail |

## Output screenshots
There are **3** different way to output files
1. Upload to **Actions Artifacts**
2. Send message to **Telegram** chat
3. Comment in **PR**

More full example in below

# Example 1
About this example:
- screenshot desktop and few specific devices
- Upload screenshots to **Actions Artifacts**

How to create new workflow
1. At the root of your repository, create a directory named `.github/workflows` to store your workflow files.
2. In `.github/workflows`, add a `.yml` or `.yaml` file for your workflow. For example, `.github/workflows/screenshots-workflow.yml`.

More workflow info:
- https://help.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow

```yaml
name: screenshots ci actions
on:
  push:
    branches:
    - master # Trigger flow when something pushed to master branch

jobs:
  screenshots:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: install puppeteer-headful
      uses: mujo-code/puppeteer-headful@master # Required for headful puppeteer
      env:
        CI: 'true'
    - name: screenshots-ci-action
      uses: Primajin/screenshots-ci-action@v3
      with:
        url: https://github.com
        devices: iPhone 6,iPhone 6 landscape,Nexus 7,Pad Pro,Galaxy S III landscape,iPad Pro landscape
    - uses: actions/upload-artifact@v4 # Uplaod screenshots to Actions Artifacts via actions/upload-artifact@v4
      with:
        path: screenshots
        name: Download-screenshots
```

# Example 2(screenshot iphone 6, without desktop)

```yaml
name: screenshots ci actions
on:
  push:
    branches:
    - master

jobs:
  screenshots:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: install puppeteer-headful
      uses: mujo-code/puppeteer-headful@master
      env:
        CI: 'true'
    - name: screenshots-ci-action
      uses: Primajin/screenshots-ci-action@v3
      with:
        url: https://github.com
        devices: iPhone 6,iPhone 6 landscape
        noDesktop: true
    - uses: actions/upload-artifact@v4
      with:
        path: screenshots
        name: Download-screenshots
```

# Example 3 (multi URLs)
- URL 1: https://www.facebook.com/ (desktop)
- URL 2: https://m.facebook.com/   (mobile (iPhone 6))

```yaml
name: screenshots ci actions
on:
  push:
    branches:
    - master

jobs:
  screenshots:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: install puppeteer-headful
      uses: mujo-code/puppeteer-headful@master
      env:
        CI: 'true'
    - name: screenshots-desktop-facebook
      uses: Primajin/screenshots-ci-action@v3
      with:
        url: https://www.facebook.com/
    - uses: actions/upload-artifact@v4
      with:
        path: screenshots
        name: Download-desktop-screenshots # Put URL1 screenshots into Download-desktop-screenshots
    - run: rm ./screenshots/* # Remove URL1 files

    - name: screenshots-mobile-facebook
      uses: Primajin/screenshots-ci-action@v3
      with:
        url: https://m.facebook.com/
        devices: iPhone 6,iPhone 6 landscape
        noDesktop: true
    - uses: actions/upload-artifact@v4
      with:
        path: screenshots
        name: Download-mobile-screenshots # Put URL2 screenshots into Download-desktop-screenshots
```

# More examples (**Vercel**, **Netlify**)
- [**Vercel** Preview Deployment screenhots](/README.Vercel.md)
- [**Netlify** Preview Deployment screenhots](/README.Netlify.md)
- [Integration with **Telegram**](/README.Telegram.md)
- [Post screenshots into **PR comment**](/README.PR.md)
- [Manually dispatch workflow](/README.workflow-dispatch.md)

# Download screenshots (more result example in below)
![img](./asset/download_screenshots_01.jpg)
![img](./asset/download_screenshots_02.jpg)

# Telegram
![img](./asset/telegram4.jpg)

## Desktop: auto-create all of these ratio
- width: 540px, height: 405px
- width: 600px, height: 450px
- width: 720px, height: 540px
- width: 960px, height: 720px
- width: 1140px, height: 640px
- width: 1280px, height: 720px
- width: 1920px, height: 1080px

### Supported mobile devices

The `devices` input accepts any name from puppeteer's
[`KnownDevices`](https://pptr.dev/api/puppeteer.knowndevices). Pass the base
name for portrait, or append ` landscape` for the landscape orientation
(e.g. `iPad Pro landscape`). puppeteer's list is the source of truth and is
kept current by upgrading puppeteer in this fork.

<details>
<summary>All 66 base devices (as of puppeteer v24 — 131 orientations)</summary>

`Blackberry PlayBook`
`BlackBerry Z30`
`Galaxy Note 3`
`Galaxy Note II`
`Galaxy S III`
`Galaxy S5`
`Galaxy S8`
`Galaxy S9+`
`Galaxy Tab S4`
`iPad`
`iPad (gen 6)`
`iPad (gen 7)`
`iPad Mini`
`iPad Pro`
`iPad Pro 11`
`iPhone 4`
`iPhone 5`
`iPhone 6`
`iPhone 6 Plus`
`iPhone 7`
`iPhone 7 Plus`
`iPhone 8`
`iPhone 8 Plus`
`iPhone SE`
`iPhone X`
`iPhone XR`
`iPhone 11`
`iPhone 11 Pro`
`iPhone 11 Pro Max`
`iPhone 12`
`iPhone 12 Pro`
`iPhone 12 Pro Max`
`iPhone 12 Mini`
`iPhone 13`
`iPhone 13 Pro`
`iPhone 13 Pro Max`
`iPhone 13 Mini`
`iPhone 14`
`iPhone 14 Plus`
`iPhone 14 Pro`
`iPhone 14 Pro Max`
`iPhone 15`
`iPhone 15 Plus`
`iPhone 15 Pro`
`iPhone 15 Pro Max`
`JioPhone 2`
`Kindle Fire HDX`
`LG Optimus L70`
`Microsoft Lumia 550`
`Microsoft Lumia 950`
`Nexus 10`
`Nexus 4`
`Nexus 5`
`Nexus 5X`
`Nexus 6`
`Nexus 6P`
`Nexus 7`
`Nokia Lumia 520`
`Nokia N9`
`Pixel 2`
`Pixel 2 XL`
`Pixel 3`
`Pixel 4`
`Pixel 4a (5G)`
`Pixel 5`
`Moto G4`

Append ` landscape` to any of the above for its landscape variant.

</details>

## Result examples
### desktop (600x450)
![img](./asset/desktopPage600x450-6febe96.png)

### desktop (960x720)
![img](./asset/desktopPage960x720-6febe96.png)

### desktop (1140x640)
![img](./asset/desktopPage1140x640-6febe96.png)

### desktop (1920x1080)
![img](./asset/desktopPage1920x1080-6febe96.png)

### mobile (iPhone_6_landscape)
![img](./asset/iPhone_6_landscape-6febe96.png)

### mobile (iPhone_6)
![img](./asset/iPhone_6-6febe96.png)

### mobile (Nexus_7)
![img](./asset/Nexus_7-6febe96.png)

### mobile (iPhone_6, fullPage)
<p align="center">
  <img width="auto" height="450" src="asset/iPhone_6-bf5fcab-fullPage.jpeg"
</p>
