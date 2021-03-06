# React Native Android Sqlite

A react native android wrapper for SQLite

## Rationale

React Native doesn't have a built-in module to access Sqlite databases, either in iOS or Android.

This library intends to fill the gap on the Android side.

## Setup

* Install Module

```bash
npm install --save-dev react-native-android-sqlite
```

* `android/settings.gradle`

```gradle
...
include ':react-native-android-sqlite'
project(':react-native-android-sqlite').projectDir = new File(settingsDir, '../node_modules/react-native-android-sqlite')
```

* `android/app/build.gradle`

```gradle
dependencies {
	...
	compile project(':react-native-android-sqlite')
}
```

* register module (in MainActivity.java)

```java
...

import io.jbrodriguez.react.*; // <--- import 

public class MainActivity extends Activity implements DefaultHardwareBackBtnHandler {
	...
	
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mReactRootView = new ReactRootView(this);

        mReactInstanceManager = ReactInstanceManager.builder()
                .setApplication(getApplication())
                .setBundleAssetName("index.android.bundle")
                .setJSMainModuleName("index.android")
                .addPackage(new MainReactPackage())
                .addPackage(new RNSQLiteModule())           // <- add here
                .setUseDeveloperSupport(BuildConfig.DEBUG)
                .setInitialLifecycleState(LifecycleState.RESUMED)
                .build();

        mReactRootView.startReactApplication(mReactInstanceManager, "YourProject", null);

        setContentView(mReactRootView);
    }	
}
```

## Usage

This library depends on [SQLiteAssetHelper](https://github.com/jgilfelt/android-sqlite-asset-helper).

The idea is that you `import` your previously created database as an application asset.

SQLiteAssetHelper manages schema definition (create), as well as upgrades.

For more information refer to [SQLiteAssetHelper's docs](https://github.com/jgilfelt/android-sqlite-asset-helper) and/or check [SQLiteOpenHelper](http://developer.android.com/reference/android/database/sqlite/SQLiteOpenHelper.html)

So, the first step involves copying your sqlite db to the following folder

```
<YourProject>/android/app/src/main/assets/databases
```
Substitute `<YourProject>` with the folder where your app resides, i.e. AwesomeProject.

> **NOTE**: Originally, I suggested copying the sqlite db to `<ReactNativeRootFolder>/node_modules/react-native-android-sqlite/src/main/assets/databases`. Although it works, the db will be erased each time you upgrade this component, so it's better to follow the updated instructions.

Having done that, you can start interacting with the db, through 4 public functions:

- init
- query
- exec
- close

### Init
The database must be initialized before any other call takes place

```js
var sqlite = require('react-native-android-sqlite')

var databaseName = 'app.db'

sqlite.init(databaseName)
	.then((_) => {
		console.log('database initialized.')
	}
)

```

### Exec
pre-requisite: the db must have been initialized

```js
var sqlite = require('react-native-android-sqlite')

var sql = 'INSERT INTO todo(name, completed) VALUES (?, ?)'
var params = ["Create react native android sqlite", 1]

sqlite.exec(sql, params)
	.then((_) => {
		console.log('row inserted.')
	}
)

```

### Query
pre-requisite: the db must have been initialized

```js
var sqlite = require('react-native-android-sqlite')

var sql = 'SELECT * FROM todo WHERE completed = ?'
var params = [1]

sqlite.query(sql, params)
	.then((data) => {
		console.log('retrieved: ', data)
	}
)
```

### Close
pre-requisite: the db must have been initialized

```js
sqlite.close()
    .then((_) => {
        console.log('database closed')
    }
)
```

## Known Issues
* It doesn't return the id for a newly inserted row (maybe create a separate insert function ?)
* Column types currently supported are Integer and String
* Additional error handling should be implemented
* Although I'm using it in my personal projects, it's still an early release. Please do read the [license](https://github.com/jbrodriguz/react-native-android-sqlite/README.md)

## Changes
Please submit any PR's you seem fit.

## Credits
* [React Native](https://facebook.github.io/react-native/) - Awesome software.
* [Android SQLiteAssetHelper](https://github.com/jgilfelt/android-sqlite-asset-helper) - Simplifying the handling of Android's sqlite interface
* [React Native Android Badge](https://github.com/jhen0409/react-native-android-badge) - For showing me the light with regards to the gradle build system, as applied to react native

## LICENSE

MIT