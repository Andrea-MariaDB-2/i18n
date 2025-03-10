# Selenium and WebDriver

Aus [ChromeDriver - WebDriver for Chrome][chrome-driver]:

> WebDriver ist ein Open-Source-Tool zum automatisierten Testen von Webanwendungen in vielen Browsern. Es bietet Funktionen für die Navigation zu Webseiten, Benutzereingaben, JavaScript-Ausführung und mehr. ChromeDriver ist ein eigenständiger Server, der das Wire-Protokoll vom WebDriver für Chromium implementiert. Es wird von Mitgliedern des Chromium- und WebDriver-Teams entwickelt.

## Spectron einrichten

[Spectron][spectron] ist das offiziell unterstützte ChromeDriver Test Framework für Electron. Es basiert auf [WebdriverIO](https://webdriver.io/) und hat Helfer, um auf die Electron APIs in Ihren Tests zuzugreifen und ChromeDriver zu bündeln.

```sh
$ npm install --save-dev spectron
```

```javascript
// A simple test to verify a visible window is opened with a title
const Application = require('spectron').Application
const assert = require('assert')

const myApp = new Application({
  path: '/Applications/MyApp.app/Contents/MacOS/MyApp'
})

const verifyWindowIsVisibleWithTitle = async (app) => {
  await app.start()
  try {
    // Check if the window is visible
    const isVisible = await app.browserWindow.isVisible()
    // Verify the window is visible
    assert.strictEqual(isVisible, true)
    // Get the window's title
    const title = await app.client.getTitle()
    // Verify the window's title
    assert.strictEqual(title, 'My App')
  } catch (error) {
    // Log any failures
    console.error('Test failed', error.message)
  }
  // Stop the application
  await app.stop()
}

verifyWindowIsVisibleWithTitle(myApp)
```

## Einrichten mit WebDriverJs

[WebDriverJs](https://www.selenium.dev/selenium/docs/api/javascript/index.html) provides a Node package for testing with web driver, we will use it as an example.

### 1. Start ChromeDriver

Zuerst müssen Sie das `chromedriver`-Binary herunterladen und ausführen:

```sh
$ npm install electron-chromedriver
$ ./node_modules/.bin/chromedriver
Starting ChromeDriver (v2.10.291558) on port 9515
Only local connections are allowed.
```

Merken Sie sich die Portnummer `9515`, die später verwendet wird.

### 2. Install WebDriverJS

```sh
$ npm install selenium-webdriver
```

### 3. Connect to ChromeDriver

Die Verwendung von `selenium-webdriver` mit Electron ist die gleiche wie bei Upstream, nur dass Sie manuell angeben müssen, wie Sie den Chromtreiber anschließen und wo Sie die Binärdatei von Electron finden:

```javascript
const webdriver = require('selenium-webdriver')

const driver = new webdriver.Builder()
  // The "9515" is the port opened by chrome driver.
  .usingServer('http://localhost:9515')
  .withCapabilities({
    'goog:chromeOptions': {
      // Here is the path to your Electron binary.
      binary: '/Path-to-Your-App.app/Contents/MacOS/Electron'
    }
  })
  .forBrowser('chrome') // note: use .forBrowser('electron') for selenium-webdriver <= 3.6.0
  .build()

driver.get('http://www.google.com')
driver.findElement(webdriver.By.name('q')).sendKeys('webdriver')
driver.findElement(webdriver.By.name('btnG')).click()
driver.wait(() => {
  return driver.getTitle().then((title) => {
    return title === 'webdriver - Google Search'
  })
}, 1000)

driver.quit()
```

## Einrichten mit WebdriverIO

[WebdriverIO](https://webdriver.io/) provides a Node package for testing with web driver.

### 1. Start ChromeDriver

Zuerst müssen Sie das `chromedriver`-Binary herunterladen und ausführen:

```sh
$ npm install electron-chromedriver
$ ./node_modules/.bin/chromedriver --url-base=wd/hub --port=9515
Starting ChromeDriver (v2.10.291558) on port 9515
Only local connections are allowed.
```

Merken Sie sich die Portnummer `9515`, die später verwendet wird.

### 2. Install WebdriverIO

```sh
$ npm install webdriverio
```

### 3. Connect to chrome driver

```javascript
const webdriverio = require('webdriverio')
const options = {
  host: 'localhost', // Use localhost as chrome driver server
  port: 9515, // "9515" is the port opened by chrome driver.
  desiredCapabilities: {
    browserName: 'chrome',
    'goog:chromeOptions': {
      binary: '/Path-to-Your-App/electron', // Path to your Electron binary.
      args: [/* cli arguments */] // Optional, perhaps 'app=' + /path/to/your/app/
    }
  }
}

const client = webdriverio.remote(options)

client
  .init()
  .url('http://google.com')
  .setValue('#q', 'webdriverio')
  .click('#btnG')
  .getTitle().then((title) => {
    console.log('Title was: ' + title)
  })
  .end()
```

## Workflow

Um Ihre Anwendung ohne Neuaufbau von Electron zu testen, [platzieren](https://github.com/electron/electron/blob/master/docs/tutorial/application-distribution.md) Sie Ihre App-Quelle in das Ressourcenverzeichnis von Electron.

Alternatively, pass an argument to run with your Electron binary that points to your app's folder. This eliminates the need to copy-paste your app into Electron's resource directory.

[chrome-driver]: https://sites.google.com/a/chromium.org/chromedriver/
[spectron]: https://electronjs.org/spectron
