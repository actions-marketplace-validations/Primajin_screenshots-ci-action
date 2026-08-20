# waitForSelector example



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
        url: https://flameddd.github.io/screenshots-ci-action-test-waitForSelector/
        noDesktop: true
        devices: iPhone 6
        waitForSelector: '#waitForElement'
        waitForSelectorTimeout: 5000
    - uses: actions/upload-artifact@v4
      with:
        path: screenshots
        name: Download-screenshots
```
