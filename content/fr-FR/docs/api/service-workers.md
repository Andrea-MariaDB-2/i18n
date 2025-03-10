## Class: ServiceWorkers

> Query and receive events from a sessions active service workers.

Processus : [Main](../glossary.md#main-process)

Les instances de la classe `ServiceWorkers` sont accessibles à l'aide de la propriété `serviceWorkers` d'une `Session`.

Par exemple :

```javascript
const { session } = require('electron')

// Get all service workers.
console.log(session.defaultSession.serviceWorkers.getAllRunning())

// Handle logs and get service worker info
session.defaultSession.serviceWorkers.on('console-message', (event, messageDetails) => {
  console.log(
    'Got service worker message',
    messageDetails,
    'from',
    session.defaultSession.serviceWorkers.getFromVersionID(messageDetails.versionId)
  )
})
```

### Événements d’instance

Les événements suivants sont disponibles pour les instances de `ServiceWorkers` :

#### Événement : 'console-message'

Retourne :

* `event` Event
* `messageDetails` Object - Information about the console message
  * `message` String - The actual console message
  * `versionId` Number - The version ID of the service worker that sent the log message
  * `source` String - The type of source for this message.  Can be `javascript`, `xml`, `network`, `console-api`, `storage`, `app-cache`, `rendering`, `security`, `deprecation`, `worker`, `violation`, `intervention`, `recommendation` or `other`.
  * `level` Number - The log level, from 0 to 3. In order it matches `verbose`, `info`, `warning` and `error`.
  * `sourceUrl` String - The URL the message came from
  * `lineNumber` Number - The line number of the source that triggered this console message

Emitted when a service worker logs something to the console.

#### Event: 'registration-completed'

Retourne :

* `event` Event
* `details` Object - Information about the registered service worker
  * `scope` String - The base URL that a service worker is registered for

Emitted when a service worker has been registered. Can occur after a call to [`navigator.serviceWorker.register('/sw.js')`](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerContainer/register) successfully resolves or when a Chrome extension is loaded.

### Méthodes d’instance

Les méthodes suivants sont disponibles pour les instances de `ServiceWorkers` :

#### `serviceWorkers.getAllRunning()`

Returns `Record<Number, ServiceWorkerInfo>` - A [ServiceWorkerInfo](structures/service-worker-info.md) object where the keys are the service worker version ID and the values are the information about that service worker.

#### `serviceWorkers.getFromVersionID(versionId)`

* `versionId` Number

Returns [`ServiceWorkerInfo`](structures/service-worker-info.md) - Information about this service worker

If the service worker does not exist or is not running this method will throw an exception.
