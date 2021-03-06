CouchDroid
===========

Effortlessly sync your Android user data to and from CouchDB.

**Version 0.9-very-beta**

Introduction
-------------

### What is this?

CouchDroid is an Android adapter for [PouchDB][4].  It offers a simple key-value store, backed by SQLite, that can automatically sync with a remote [CouchDB][5] database via HTTP/HTTPS.

### Why do I care?

Syncing is hard.  You don't want to have to manage revisions, conflicts, and incremental sync yourself.  CouchDB/PouchDB will handle all that junk for you, so you can devote your brain cells to other problems.

### Why Couch/Pouch?

CouchDB is awesome.  If you use its built-in user authentication, you can write Ajax apps with barely any server code at all (if any).  PouchDB is awesome too - it runs cross-browser, and it offers automagical two-way sync between the client and CouchDB.

### How does this work?

Rather than rewrite PouchDB in Java, CouchDroid fires up an invisible WebView, which it basically uses as a JavaScript interpreter to run PouchDB with its WebSQL adapter.  Calls to WebSQL are rerouted to the native Android SQLite API, while calls to XMLHttpRequest are rerouted to the Apache HttpClient API.  [Jackson][6] is used for JSON serialization/deserialization.

### Isn't the performance terrible?

Not really.  Since most of the heavy lifting is done in SQLite/HTTP, relatively little code is executed on the UI thread.  It even manages to run on my vintage HTC Magic (2008) rocking Android 2.1 Eclair.  And with Chrome included as the standard WebView in 4.4 KitKat, it's only gonna get faster.

### Why not just use Cordova/PhoneGap?

I thought it would be overkill to include all the Cordova libraries.  Plus, standard Android Cordova apps run the WebSQL requests on the UI thread, meaning that even a spinning progress bar would stutter (PSA: there's [a Cordova plugin for that][1]).  And getting Ajax to work would require tedious configuration of CORS/JSONP to get around web security, whereas CouchDroid works on a freshly-installed CouchDB.

Also CouchDroid doesn't have any external dependencies, other than PouchDB and Jackson.  The APK clocks in at about 700K (400K with ProGuard).

### But I already store user data in SQLite!

CouchDroid includes a small utility called CouchDroidMigrationTask, which will migrate your existing SQLite tables into a reasonable key-value format.  So, if you don't want to dive head-first into Pouch, you can use it purely for one-way sync to CouchDB.

Limitations
-----------

1. CouchDroid needs a WebView in order to run JavaScript.  Hence, you can't use it in a background Service, and it does consume UI thread cycles.  For small databases, though, you probably won't notice.
1. The PouchDB API is asynchronous. This means that JavaScript-style callback hell has invaded your Java cathedral.  Just avoid [the pyramid of doom][3] and you should be fine.
1. Android 2.1 (API level 7) and up is supported.

Details
----------

More details in [this blog post](http://nolanlawson.com/2013/12/08/porting-pouchdb-to-android-initial-work-and-thoughts/).

<!--
Scenarios
----------

### 1. You already have SQLite user data, and you want to sync it to CouchDB.

The CouchDroidMigrationTask tool is designed specifically for this.  It will copy the existing SQLite tables and write it to a new PouchDB (overwriting if necessary).

Once you have a ```PouchDB``` object, you can set up a one-way replication to CouchDB.  Of course, it will only send the diffs, and only when the service is available.  (Did I mention PouchDB is awesome?)

### 2. You're writing a new app, and you want to use pure CouchDroid.

It's just PouchDB!  Follow the PouchDB APIs, which have been translated as faithfully as possible into Java.

CouchDroid uses Jackson for JSON serialization/deserialization, which means that, for the most part, POJOs will "just work."
-->
License
----------

Apache 2.0

Author
--------
Nolan Lawson

[1]: https://github.com/pgsqlite/PG-SQLitePlugin-Android-2013.09
[2]: http://guide.couchdb.org/draft/conflicts.html
[3]: http://tritarget.org/blog/2012/11/28/the-pyramid-of-doom-a-javascript-style-trap/]
[4]: http://pouchdb.com/
[5]: http://couchdb.apache.org/
[6]: http://jackson.codehaus.org/
