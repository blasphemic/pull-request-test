## OneCMS Code Library

**ATEX INTERNAL USE ONLY. Not intended for distribution**

**Only changes that should apply to ALL OneCMS applications should be made here.**

If you are seeing this repository and are not part of the Atex organization, please mail
[Team Lost Ninjas](mailto:Team-Lost.STO@atex.com) and tell us.

### About

This is the source code repository of the Atex OneCMS Code Library, containing code intended to be useful
to all current and future OneCMS applications. It is divided into two parts, one core part and one AngularJS part.
The AngularJS part of the OneCMS Code Library essentially wraps the core parts in AngularJS services.

**The OneCMS Code Library also includes the code for the OneCMS UI Framework, but this might change in the future. For documentation on the OneCMS UI Framework, please see http://support.polopoly.com/confluence/display/PolopolyMaster/OneCMS+UI+Framework.**

If you have any questions, contact [Team Lost Ninjas](mailto:Team-Lost.STO@atex.com).

### Table of contents

* [Using](#using)
  * [Code JAR file contents](#code-jar-file-contents)
  * [Referencing the OneCMS Code Library in your code](#referencing-the-library)
  * [Dependencies](#dependencies)
* [Exposed functionality - Core](#functionality-core)
  * [atex.onecms.service.AuthenticationService](#atex-onecms-service-authenticationservice)
    * validate(authToken)
    * authenticate(username, password)
    * invalidate(authToken)
  * [atex.onecms.service.ContentService](#atex-onecms-service-contentservice)
    * get(transferInfo)
    * create(transferInfo)
    * update(transferInfo)
  * [atex.onecms.service.SearchService](#atex-onecms-service-searchservice)
    * search(searchInfo)
  * [atex.onecms.service.TypeService](#atex-onecms-service-typeservice)
    * get(transferInfo)
  * [atex.onecms.service.FileService](#atex-onecms-service-fileservice)
    * getFileAsBlob(transferInfo)
    * putFile(transferInfo)
  * [atex.onecms.AngularUtils](#atex-onecms-angularutils)
    * apply(scope, fn)
    * allSettled(promises)
    * splitSettled(allSettledResult)
    * applyingQ(qLib, scope)
  * [atex.onecms.ObjectUtils](#atex-onecms-objectutils)
    * getByPath(object, path)
  * [atex.onecms.error.Error](#atex-onecms-error-error)
  * [atex.onecms.error.types](#atex-onecms-error-types)
  * [atex.onecms.error.severities](#atex-onecms-error-severities)
* [Exposed functionality - AngularJS](#functionality-angularjs)
  * [Events](#angularjs-events)
    * [onecms:authenticationRequest](#event-onecms-authenticationRequest)
    * [onecms:authenticationSuccess](#event-onecms-authenticationSuccess)
  * [Module atex.onecms.application](#module-atex-onecms-application)
    * [Service 'Application'](#service-application)
      * reset()
      * setCookies()
      * currentUser
    * [Service 'ApplicationConfiguration'](#service-applicationconfiguration)
      * getConfigurationFor(configurationPath)
  * [Module atex.onecms.authentication](#module-atex-onecms-authentication)
    * [Service 'AuthenticationDialog'](#service-authenticationdialog)
      * create(params)
  * [Module atex.onecms.service](#module-atex-onecms-service)
    * [Service 'AuthenticationService'](#service-authenticationservice)
      * confirmAuthenticationRequest()
      * cancelAuthenticationRequest()
      * callWithAuthentication(fn)
      * getCurrentUser()
      * isLoggedIn()
      * login(username, password)
    * [Service 'ContentService'](#service-contentservice)
      * getContent(transferInfo)
      * createContent(transferInfo)
      * updateContent(transferInfo)
    * [Service 'SearchService'](#service-searchservice)
      * search(searchInfo)
    * [Service 'TypeService'](#service-typeservice)
      * get(transferInfo)
    * [Service 'FileService'](#service-fileservice)
      * getFileAsBlob(transferInfo)
      * putFile(transferInfo)
* [Changelog](#changelog)

<a name="using" />
### Using

The Atex OneCMS Code Library is distributed as two Maven artifacts. The first one is a plain JAR file containing the Atex OneCMS Code Library itself. It is depended upon like this:

```xml
<dependency>
  <groupId>com.atex</groupId>
  <artifactId>onecms-lib</artifactId>
  <version>VERSION</version>
</dependency>
```

**Please note that for this to work, your application must be deployed and running in a Servlet 3.0-compatible web container**.

The second Maven artifact is a contentdata JAR which is intended for import into a Polopoly system. It contains domain objects and widgets that are included in the UI Framework. It is depended upon like this:

```xml
<dependency>
  <groupId>com.atex</groupId>
  <artifactId>onecms-lib</artifactId>
  <version>VERSION</version>
  <classifier>contentdata</classifier>
</dependency>
```

**This dependency only makes sense in a Polopoly project where it can be properly imported.**

<a name="code-jar-file-contents" />
#### Code JAR file contents

The code JAR contains the following files:

```
./META-INF/resources/atex/onecms-lib/VERSION/onecms-lib-service-angular-VERSION-min.js
./META-INF/resources/atex/onecms-lib/VERSION/onecms-lib-service-angular-VERSION-src.js
./META-INF/resources/atex/onecms-lib/VERSION/onecms-lib-service-core-VERSION-min.js
./META-INF/resources/atex/onecms-lib/VERSION/onecms-lib-service-core-VERSION-src.js
./META-INF/resources/atex/onecms-lib/VERSION/onecms-lib-ui-framework-angular-VERSION-min.js
./META-INF/resources/atex/onecms-lib/VERSION/onecms-lib-ui-framework-angular-VERSION-src.js
./META-INF/resources/atex/onecms-lib/VERSION/onecms-lib-ui-framework-core-VERSION-min.js
./META-INF/resources/atex/onecms-lib/VERSION/onecms-lib-ui-framework-core-VERSION-src.js
```

The code contained in the JAR is divided in a couple of different ways:

* First, the code is divided into files suffixed with `-service-` and `-ui-framework-`. The `-service-` files contain code for the OneCMS services as well as general OneCMS functionality (error handling, etc). The `-ui-framework-` files contain code for the OneCMS UI Framework. Please note that the code in these parts are for completely separated areas of the OneCMS Code Library, and if you want to utilize functionality from both, you will have to include both. Also, note that the OneCMS UI Framework code (the `-ui-framework-` files) requires that the `-service-` code files has been loaded already.
* Second, the files suffixed with `-angular-` also contain their `-core-` counterparts, so if you are using AngularJS you don't need to include both files. Both the `-angular-` version and the `-core-` version comes in minified and non-minified versions.
* Third, there are `-min-` and `-src-` versions of all files. The `-min-` files are minified while the `-src-` files are not.

<a name="referencing-the-library" />
#### Referencing the OneCMS Code Library in your code

After adding the dependency to your project's top pom.xml file, you can reference the files like this:

```html
<script src="atex/onecms-lib/VERSION/onecms-lib-service-angular-VERSION.min.js"></script>
<script src="atex/onecms-lib/VERSION/onecms-lib-ui-framework-angular-VERSION.min.js"></script>
```

In this example we are using the AngularJS versions of the OneCMS Code Library, so we also need to declare module dependencies in our application module. For example:

```javascript
angular.module('my-app-module', ['atex.onecms.authentication', 'atex.onecms.service']);
```

<a name="dependencies" />
### Dependencies

The OneCMS Code Library depends on certain libraries already being present at load-time. The core part requires the following libraries:

* jQuery 1.9.x
* Underscore 1.4.x
* Q 0.9.x

The AngularJS part requires the following libraries:

* jQuery 1.9.x
* Underscore 1.4.x
* AngularJS 1.2.x
* onecms-theme 3.3.3

<a name="functionality-core" />
### Exposed functionality - Core

The core part of the OneCMS Code Library consists of low-level implementations for server-side services such as the Content REST API and the Authentication service.

##### General rules of thumb:

* One implementation per server-side service.
* All functionality return Q-based promises.
* Returned promises are resolved with return data of the operation when the operation succeeds.
* Returned promises are rejected using atex.onecms.error.Error objects when the operation fails.

<a name="atex-onecms-service-authenticationservice" />
#### atex.onecms.service.AuthenticationService

The `AuthenticationService` object is a low-level service that handles authentication with the server. `atex.onecms.service.AuthenticationService` is a function returning an instance of the `AuthenticationService` API:

```javascript
/**
 * Pseudo-implementation of the atex.onecms.service.AuthenticationService:
 *
 * Argument 'authenticationServiceUrl' - The URL to the authentication service in the Content REST API.
 * Argument 'serviceSettings' - An object containing settings for this service.
 *
 * The 'serviceSettings' object should look like the following:
 *
 * {
 *   customQ: customQObject
 * }
 *
 * Property 'customQ' is an implementation of a promise based Q library,
 * i.e Angular $q. Defaults to a global Q.
 */
atex.onecms.service.AuthenticationService = function(authenticationServiceUrl, serviceSettings) {
  return {

    /**
     * Verifies whether the given authentication token is still valid.
     *
     * Argument 'authToken' - The authentication token to verify.
     *
     * Returns a Q-based promise.
     *
     * Resolves the returned promise with an object like:
     *
     * {
     *   userId: 'USERID',
     *   token: 'TOKEN'
     * }
     *
     * if the operation succeeds.
     *
     * Property 'userId' is the user ID of the user contained in the token.
     * Property 'token' is the input token.
     *
     * Rejects the returned promise with an atex.onecms.error.Error object if the
     * operation fails.
     */
    validate: function (authToken) {
      // ...
    },

    /**
     * Authenticates using the given username and password.
     *
     * Argument 'username' - The username to authenticate with.
     * Argument 'password' - The password to authenticate with.
     *
     * Returns a Q-based promise.
     *
     * Resolves the returned promise with an object like:
     *
     * {
     *   userId: 'USERID',
     *   token: 'TOKEN'
     * }
     *
     * if the operation succeeds.
     *
     * Property 'userId' is the user ID of the user that was just authenticated.
     * Property 'token' is the resulting authentication token.
     *
     * Rejects the returned promise with an atex.onecms.error.Error object if the
     * operation fails.
     */
    authenticate: function (username, password) {
      // ...
    },

    /**
     * Invalidates the given authentication token.
     *
     * Argument 'authToken' - The authentication token to invalidate.
     *
     * Returns a Q-based promise.
     *
     * Resolves the returned promise with no value if the operation succeeds.
     * Rejects the returned promise with an atex.onecms.error.Error object if the
     * operation fails.
     */
    invalidate: function (authToken) {
      // ...
    }

  };
};
```

##### Usage

```javascript
/**
 * Obtain an instance of the AuthenticationService API and perform authentication
 * operations against the server.
 */

var authService = atex.onecms.service.AuthenticationService(URL_TO_CONTENT_API_AUTHENTICATION_SERVICE);

authService.validate('3000::g0S0W3IZTdr0MLyJxkXih7ycCrMi').then(function (tokenData) {
  // The given token is valid...
}, function (error) {
  // The operation failed. Inspect the 'error' object (type atex.onecms.error.Error)
  // for more information.
});

authService.authenticate('edmund', 'edmund').then(function (tokenData) {
  var authToken = tokenData.token;
  var userId = tokenData.userId;

  // Do something with the authentication information...
}, function (error) {
  // The operation failed. Inspect the 'error' object (type atex.onecms.error.Error)
  // for more information.
});

authService.invalidate('3000::g0S0W3IZTdr0MLyJxkXih7ycCrMi').then(function () {
  // The given token is now invalid...
}, function (error) {
  // The operation failed. Inspect the 'error' object (type atex.onecms.error.Error)
  // for more information.
});
```

<a name="atex-onecms-service-contentservice" />
#### atex.onecms.service.ContentService

The `ContentService` object is a low-level service that is used to read and write content to the server. `atex.onecms.service.ContentService` is a function returning an instance of the `ContentService` API:

```javascript
/**
 * Pseudo-implementation of the atex.onecms.service.ContentService.
 *
 * Argument 'contentServiceUrl' - The URL to the content service in the Content REST API.
 * Argument 'serviceSettings' - An object containing settings for this service.
 *
 * The 'serviceSettings' object should look like the following:
 *
 * {
 *   customQ: customQObject,
 *   cacheBusting: boolean
 * }
 *
 * Property 'customQ' is an implementation of a promise based Q library,
 * i.e Angular $q. Defaults to a global Q.
 *
 * Property 'cacheBusting' indicates whether cache busting strategies
 * should be used when reading content from the server in order to get around
 * browser caching bugs (read: Safari). Defaults to false.
 */
atex.onecms.service.ContentService = function(contentServiceUrl, serviceSettings) {
  return {

    /**
     * Reads the content represented by the given transferInfo object.
     *
     * Argument 'transferInfo' - An object containing information on the content
     * to read. The object should look like the following:
     *
     * {
     *   id: 'CONTENTID',
     *   variant: 'VARIANT',
     *   authToken: 'TOKEN'
     * }
     *
     * Property 'id' is the ID of the content to read.
     * Property 'variant' is the Content API variant to use when reading the content.
     * Property 'authToken' is the authentication token to use when reading the content.
     *
     * Returns a Q-based promise.
     *
     * Resolves the returned promise with an object like:
     *
     * {
     *   id: 'CONTENTID',
     *
     *   data: {
     *     contentData: {
     *        _type: 'TYPE',
     *        name: 'TITLE'
     *     },
     *
     *     otherAspect: { ... }
     *   },
     *
     *   version: 'VERSION',
     *   type: 'TYPE',
     *
     *   meta: { ... }
     * }
     *
     * if the operation succeeds.
     *
     * Property 'id' is the ID of the read content.
     *
     * Property 'data' is the data shuttle as returned from the Content REST API. Includes both the main aspect
     * as well as any other aspects associated with the requested content.
     *
     * Property 'version' is the version of the read content.
     * Property 'type' is the type of the read content.
     * Property 'meta' consists of relevant metadata for the read content.
     *
     * Rejects the returned promise with an atex.onecms.error.Error object if the
     * operation fails.
     */
    get: function (transferInfo) {
      // ...
    },

    /**
     * Creates a new content represented by the given transferInfo object.
     *
     * Argument 'transferInfo' - An object containing information on the content
     * to create. The object should look like the following:
     *
     * {
     *   data: {
     *     contentData: {
     *       _type: 'TYPE',
     *       name: 'TITLE'
     *     },
     *
     *     otherAspect: { ... }
     *   },
     *
     *   authToken: 'TOKEN'
     * }
     *
     * Property 'data' is the data shuttle of the content to create.
     * It should include the aspects that you want to include when creating the content.
     *
     * Property 'authToken' is the authentication token to use when creating the
     * content.
     *
     * Returns a Q-based promise.
     *
     * Resolves the returned promise with an object like:
     *
     * { id: 'CONTENTID' }
     *
     * if the operation succeeds.
     *
     * Property 'id' is the ID of the created content.
     *
     * Rejects the returned promise with an atex.onecms.error.Error object if the
     * operation fails.
     */
    create: function (transferInfo) {
      // ...
    },

    /**
     * Updates an existing content represented by the given transferInfo object.
     *
     * Argument 'transferInfo' - An object containing information on the content
     * to update. The object looks like:
     *
     * {
     *   id: 'CONTENTID',
     *
     *   data: {
     *     contentData: {
     *       _type: 'TYPE',
     *       name: 'TITLE'
     *     },
     *
     *     otherAspect: { ... }
     *   },
     *
     *   version: 'VERSION',
     *   authToken: 'TOKEN'
     * }
     *
     * Property 'id' is the ID of the content to update.
     *
     * Property 'data' is the data shuttle of the content to update.
     * It should include the aspects that you want to update.
     *
     * Property 'version' is the version of the content as far as the client is aware.
     * Property 'authToken' is the authentication token to use when updating the content.
     *
     * Returns a Q-based promise.
     *
     * Resolves the returned promise with an object like:
     *
     * { id: 'CONTENTID' }
     *
     * if the operation succeeds.
     *
     * Property 'id' is the ID of the updated content.
     *
     * Rejects the returned promise with an atex.onecms.error.Error object if the
     * operation fails.
     */
    update: function (transferInfo) {
      // ...
    }

  };
};
```

##### Usage

```javascript
/**
 * Obtain an instance of the ContentService API and perform content operations
 * against the server.
 */

var contentService = atex.onecms.service.ContentService(URL_TO_CONTENT_API_CONTENT_SERVICE);

var readTransferInfo = {
  id: '1.154',
  variant: 'VARIANT',
  authToken: '3000::g0S0W3IZTdr0MLyJxkXih7ycCrMi' // previously acquired token
};

contentService.get(readTransferInfo).then(function (responseData) {
  var data = responseData.data;
  var version = responseData.version;
  var type = responseData.type;

  // Do something with the data...
}, function (error) {
  // The operation failed. Inspect the 'error' object (type atex.onecms.error.Error)
  // for more information.
});

var createTransferInfo = {
  data: {
    contentData: {
      _type: 'TYPE',
      name: 'My Article'
    }
  },

  authToken: '3000::g0S0W3IZTdr0MLyJxkXih7ycCrMi'
};

contentService.create(createTransferInfo).then(function (responseData) {
  var contentId = responseData.id;

  // Do something with the id...
}, function (error) {
  // The operation failed. Inspect the 'error' object (type atex.onecms.error.Error)
  // for more information.
});

var updateTransferInfo = {
  id: '1.154',

  data: {
    contentData: {
      _type: 'TYPE',
      name: 'My Article'
    }
  },

  version: 'contentData#policy:1.200:1398676898',
  authToken: '3000::g0S0W3IZTdr0MLyJxkXih7ycCrMi'
};

contentService.update(updateTransferInfo).then(function (responseData) {
  var contentId = responseData.id;

  // Do something with the id...
}, function (error) {
  // The operation failed. Inspect the 'error' object (type atex.onecms.error.Error)
  // for more information.
});
```

<a name="atex-onecms-service-searchservice"/>
#### atex.onecms.service.SearchService

The `SearchService` object is a low-level service that is used to search for content on the server.
The service is essentially a Solr proxy with a few limitations and a few extra features.
See the [Content REST API documentation](http://support.polopoly.com/confluence/display/PolopolyMaster/Searching+with+the+REST+API)
to learn more about the service. `atex.onecms.service.SearchService` is a function returning
an instance of the `SearchService` API:

```javascript
/**
 * Pseudo-implementation of the atex.onecms.service.SearchService.
 *
 * Argument 'searchServiceUrl' - The URL to the search service in the Content REST API.
 * Argument 'serviceSettings' - An object containing settings for this service.
 *
 * The 'serviceSettings' object should look like the following:
 *
 * {
 *   customQ: customQObject
 * }
 *
 * Property 'customQ' is an implementation of a promise based Q library,
 * i.e Angular $q. Defaults to a global Q.
 */
atex.onecms.service.SearchService = function(searchServiceUrl, serviceSettings) {
  return {

    /**
     * Performs a Solr search through the Content REST API.
     *
     * Argument 'searchInfo' - An object containing information on the search
     * to perform. The object should look like the following:
     *
     * {
     *   core: 'CORE',
     *   variant: 'VARIANT',
     *   authToken: 'TOKEN',
     *
     *   customParameter: 'CUSTOMVALUE',
     *   anotherCustomParameter: ['value', 'value2']
     * }
     *
     * Property 'core' is the Solr core against which to perform the search.
     *
     * Property 'variant' is the Content API variant to use when returning content data
     * as part of the search result.
     *
     * Property 'authToken' is the authentication token to use when searching.
     *
     * NOTE: any custom parameters included in the 'searchInfo' argument object
     * will be included as query parameters in the search request against the Content REST API.
     *
     * NOTE: custom parameters with multiple values should be declared as arrays.
     * For example, if you want a parameter 'facet.field' with the values 'text' and 'modifiedBy',
     * it should be declared as 'facet.field': ['text', 'modifiedBy']. This will result in
     * a query string like 'facet.field=text&facet.field=modifiedBy'.
     *
     * Returns a Q-based promise.
     *
     * Resolves the returned promise with an object like:
     *
     * {
     *   data: {
     *     response: {
     *       docs: [{
     *         commitId: 111111,
     *         contentId: 1.100,
     *         eventId: 1000,
     *         modifiedData: '2011-11-11T11:11:11Z',
     *
     *         _data: {
     *           // data shuttle from Polopoly here
     *         }
     *       }],
     *
     *       numFound: 1,
     *       start: 0
     *     },
     *
     *     responseHeader: {
     *       QTime: 1,
     *
     *       params: {
     *         fq: 'page:2.111',
     *         q: '*:*',
     *         rows: 100,
     *         version: "2.2"
     *       }
     *     }
     *   }
     * }
     *
     * if the operation succeeds.
     *
     * Property 'data' is the Solr response data from the search.
     *
     * Rejects the returned promise with an atex.onecms.error.Error object if the
     * operation fails.
     */
    search: function(searchInfo) {
      // ...
    }

  };
};
```

##### Usage

```javascript
/**
 * Obtain an instance of the SearchService API and search for
 * content on the server.
 */

var searchService = atex.onecms.service.SearchService(URL_TO_CONTENT_API_SEARCH_SERVICE);

var searchInfo = {
  core: 'public',
  q: 'cars',
  rows: 10,
  variant: 'VARIANT',
  authToken: '3000::dqou9H0JkApD2fpS6y4SOB1ooHex'
};

searchService.search(searchInfo).then(function(searchResponse) {
  var documents = searchResponse.data.response.docs;

  // Do something with the documents...
}, function(error) {
  // The operation failed. Inspect the 'error' object (type atex.onecms.error.Error)
  // for more information.
});
```

<a name="atex-onecms-service-typeservice"/>
#### atex.onecms.service.TypeService

The `TypeService` object is a low-level service that is used for type lookups in the Content REST API. `atex.onecms.service.TypeService` is a function returning an instance of the `TypeService` API:

```javascript
/**
 * Pseudo-implementation of the atex.onecms.service.TypeService.
 *
 * Argument 'typeServiceUrl' - The URL to the type service in the Content REST API.
 * Argument 'serviceSettings' - An object containing settings for this service.
 *
 * The 'serviceSettings' object should look like the following:
 *
 * {
 *   customQ: customQObject
 * }
 *
 * Property 'customQ' is an implementation of a promise based Q library,
 * i.e Angular $q. Defaults to a global Q.
 */
atex.onecms.service.TypeService = function(typeServiceUrl, serviceSettings) {
  return {

    /**
     * Performs a type lookup in the Content REST API.
     *
     * Argument 'transferInfo' - An object containing information on the type
     * you want to look up. The object should look like the following:
     *
     * {
     *   id: 'ID',
     *   authToken: 'TOKEN'
     * }
     *
     * Property 'id' is the id of the type you want to lookup.
     * Property 'authToken' is the authentication token to use when communicating
     * with the Content REST API.
     *
     * Returns a Q-based promise.
     *
     * Resolves the returned promise with an object like:
     *
     * {
     *   typeName: "TYPENAME",
     *
     *   attributes: [
     *     {
     *       modifiers: "3",
     *       name: "field",
     *       typeName: "java.lang.String"
     *     }
     *   ]
     * }
     *
     * if the operation succeeds.
     *
     * Property 'typeName' is the name of the returned type.
     * Property 'attributes' is a list of attributes for the returned type.
     *
     * Rejects the returned promise with an atex.onecms.error.Error object if the
     * operation fails.
     */
    get: function(transferInfo) {
      // ...
    }

  };
};
```

##### Usage

```javascript
/**
 * Obtain an instance of the TypeService API and retrieve a type definition from the server.
 */

var typeService = atex.onecms.service.TypeService(URL_TO_CONTENT_API_TYPE_SERVICE);

var transferInfo = {
  id: 'java.lang.String',
  authToken: '3000::dqou9H0JkApD2fpS6y4SOB1ooHex'
};

typeService.get(transferInfo).then(function(response) {
  // Do something with the response...
}, function(error) {
  // The operation failed. Inspect the 'error' object (type atex.onecms.error.Error)
  // for more information.
});
```

<a name="atex-onecms-service-fileservice"/>
#### atex.onecms.service.FileService

The `FileService` object is a low-level service that is used to transfer files to and from the file service in the Content REST API.
`atex.onecms.service.FileService` is a function returning an instance of the `FileService` API:

```javascript
/**
 * Pseudo-implementation of the atex.onecms.service.FileService.
 *
 * Argument 'fileServiceUrl' - The URL to the file service in the Content REST API.
 * Argument 'serviceSettings' - An object containing settings for this service.
 *
 * The 'serviceSettings' object should look like the following:
 *
 * {
 *   customQ: customQObject
 * }
 *
 * Property 'customQ' is an implementation of a promise based Q library,
 * i.e Angular $q. Defaults to a global Q.
 */
atex.onecms.service.FileService = function(fileServiceUrl, serviceSettings) {
  return {

    /**
     * Retrieves a file from the Content API File service and returns it as a
     * blob (as defined by https://developer.mozilla.org/en-US/docs/Web/API/Blob).
     *
     * Argument 'transferInfo' - An object containing information on the file
     * to retrieve. The object should look like the following:
     *
     * {
     *   uri: 'FILE URI',
     *   authToken: 'TOKEN'
     * }
     *
     * Property 'uri' is the file URI of the file to retrieve. Note that the format
     * of the parameter should be on the file URI format used by the Content API
     * File service, like scheme://host/path.
     *
     * For more information on the file URI format, please see the Content API File
     * service documentation: http://support.polopoly.com/confluence/display/PolopolyMaster/File+Service
     *
     * Property 'authToken' is the authentication token to use when retrieving the file.
     *
     * Returns an object like:
     *
     * {
     *   abort: FUNCTION,
     *   whenCompleted: FUNCTION
     * }
     *
     * Property 'abort' is a function that can be used to abort the file retrieval.
     *
     * Property 'whenCompleted' is a function that will return a Q-based promise.
     * The promise will be resolved with an object like:
     *
     * {
     *   data: BLOB
     * }
     *
     * if the operations succeeds.
     *
     * The promise will be rejected with an atex.onecms.error.Error object if the
     * operation fails or is aborted.
     *
     * Property 'data' is the response blob object containing the file data.
     *
     * For progress updates on the file retrieval, please attach an 'onProgress'
     * function on the object returned from this method. Example:
     *
     * var download = FileService.getFileAsBlob({ ... });
     * download.onProgress = function(result) { ... };
     */
    getFileAsBlob: function(transferInfo) {
      // ...
    },

    /**
     * Uploads a blob (as defined by https://developer.mozilla.org/en-US/docs/Web/API/Blob)
     * as a file to the Content API File service.
     *
     * Argument 'transferInfo' - An object containing information on the blob object
     * to upload. The object should look like the following:
     *
     * {
     *   data: BLOB,
     *   uri: 'FILE URI',
     *
     *   authToken: 'TOKEN'
     * }
     *
     * Property 'data' is the blob object to upload. Note that the Content-Type of the upload
     * request will be based on data.type (if such exists). If the blob object doesn't
     * contain any type information, 'application/octet-stream' will be used instead.
     *
     * Property 'uri' is the target file URI of the blob object to upload. Note that the
     * format of the parameter should be on the file URI format used by the Content API
     * File service, like scheme://host/path.
     *
     * For more information on the file URI format, please see the Content API File
     * service documentation: http://support.polopoly.com/confluence/display/PolopolyMaster/File+Service
     *
     * Property 'authToken' is the authentication token to use when uploading the blob object.
     *
     * Returns an object like:
     *
     * {
     *   abort: FUNCTION,
     *   whenCompleted: FUNCTION
     * }
     *
     * Property 'abort' is a function that can be used to abort the blob object upload.
     *
     * Property 'whenCompleted' is a function that will return a Q-based promise.
     * The promise will be resolved with an object like:
     *
     * {
     *   location: 'LOCATION',
     *   data: { ... }
     * }
     *
     * if the operations succeeds.
     *
     * The promise will be rejected with an atex.onecms.error.Error object if the
     * operation fails or is aborted.
     *
     * Property 'location' is an absolute URL where the uploaded blob object can be
     * downloaded as a file.
     *
     * Property 'data' is an object containing file information for the uploaded blob object.
     * Please see the Content API File service documentation for more information: http://support.polopoly.com/confluence/display/PolopolyMaster/File+Service
     *
     * For progress updates on the blob object upload, please attach an 'onProgress'
     * function on the object returned from this service. Example:
     *
     * var upload = FileService.putFile({ ... });
     * upload.onProgress = function(result) { ... };
     */
    putFile: function(transferInfo) {
      // ...
    }

  };
};
```

##### Usage

```javascript
/**
 * Obtain an instance of the FileService API and transfer a file to the server.
 */

var fileService = atex.onecms.service.FileService(URL_TO_CONTENT_API_FILE_SERVICE);

var transferInfo = {
  uri: 'tmp://',
  data: new Blob(['abc']),

  authToken: '3000::dqou9H0JkApD2fpS6y4SOB1ooHex'
};

fileService.putFile(transferInfo).whenCompleted().then(function(response) {
  // Do something with the response...
}, function(error) {
  // The operation failed. Inspect the 'error' object (type atex.onecms.error.Error)
  // for more information.
});
```

<a name="atex-onecms-angularutils"/>
#### atex.onecms.AngularUtils

The `atex.onecms.AngularUtils` object is a utility object with static AngularJS related helper methods:

```javascript
atex.onecms.AngularUtils = {
 /**
  * Forces an Angular digest cycle for the given scope if one is not
  * already in progress. Executes the given function either way.
  */
  apply: function(scope, fn) {
    // ...
  },

 /**
  * Custom implementation of Q.allSettled (since we don't use Q in
  * the Angular world, and the $q Angular service doesn't have such
  * functionality).
  */
  allSettled: function(q, promises) {
    // ...
  },

  /**
   * Takes a result from allSettled() and produce an object like:
   *
   * {
   *   fulfilled: [FULLFILLMENTS_ARRAY],
   *   rejected: [REJECTIONS_ARRAY]
   * }
   *
   */
  splitSettled: function(allSettledValue) {
    // ...
  },

  /**
   * Wraps q (can be Q or Angular $q) so that all resolve and reject
   * operations will force a digest cycle (using the apply function
   * in this util object). This is needed because AngularJS only
   * progresses down the promise chain during digests.
   */
  applyingQ: function(q, scope) {
    // ...
  }
};
```
<a name="atex-onecms-objectutils" />
#### atex.onecms.ObjectUtils

The `atex.onecms.ObjectUtils` object is a utility object with static helper methods:

```javascript
atex.onecms.ObjectUtils = {

  /**
   * Returns the data found in the given object at the given path where path is expressed as an Array
   * of keys. To get the data at object.one.two.three, use getByPath(object, ['one', 'two', 'three']).
   *
   * Parameter 'object' - The object to look into for the requested data.
   * Parameter 'path' - An array of objects that represents a location in the given object.
   *
   * Using undefined or null for either parameter will cause an error to be thrown.
   */
  getByPath: function(object, path) {
    // ...
  }
};
```

##### Usage

```javascript
var myObject = {
  'field': 'value',

  'sub.Object': {
    'sub.Field': 'subValue'
  }
};

// Accessing the values of myObject:
var fieldValue = atex.onecms.ObjectUtils.getByPath(myObject, ['field']); // --> 'value'

var subObject = atex.onecms.ObjectUtils.getByPath(myObject, ['sub.Object']); // --> { 'sub.Field': 'subValue' }
var subFieldValue = atex.onecms.ObjectUtils.getByPath(myObject, ['sub.Object', 'sub.Field']); // --> 'subValue'
```

<a name="atex-onecms-error-error" />
#### atex.onecms.error.Error

The `atex.onecms.error.Error` object represents an error in a OneCMS application. It holds information about the type of error, the cause of it, where it originated, and more. `atex.onecms.error.Error` is a function returning a new `Error` object:

```javascript
/**
 * Creates a new Error object.
 *
 * Argument 'errorInformationObject' - An optional parameter object which contains
 * error information. Any properties it contains will also be included in the returned
 * atex.onecms.error.Error object.
 *
 * If 'errorInformationObject' is not passed in, the returned atex.onecms.error.Error object
 * will look like this:
 *
 * {
 *   "date": new Date(),
 *   "cause": "An error has occurred!",
 *   "rootCause": {},
 *   "origin": "Unknown",
 *   "type": atex.onecms.error.types.UNKNOWN,
 *   "severity": atex.onecms.error.severities.NORMAL
 * }
 *
 * Returns a new Error object.
 */
atex.onecms.error.Error = function(errorInformationObject) {
  return _.extend(defaultErrorObject, errorInformationObject);
};
```

##### Usage

```javascript
/**
 * Create a new Error object with most basic properties set. Also provide
 * some custom error information that should be preserved.
 */

var error = atex.onecms.error.Error({
  origin: 'MyService',
  cause: 'Division by zero',

  type: atex.onecms.error.types.SERVER_ERROR,
  severity: atex.onecms.error.severities.NORMAL,

  /**
   * Any other information included in the errorInformationObject will also be
   * added to the atex.onecms.error.Error object
   */
  customErrorInformation: {
    errorTimestamp: '144335666644'
  },

  additionalInfo: '1.23725365'
});
```
<a name="atex-onecms-error-types" />
#### atex.onecms.error.types

The `atex.onecms.error.types` object is an enumeration of error types typically used in a OneCMS application. The `type` property of a `atex.onecms.error.Error` object will typically be one of the enumeration values.

```javascript
atex.onecms.error.types = {
  AUTHENTICATION_FAILED: 'authenticationFailed',
  CONFLICT: 'conflict',
  SERVER_ERROR: 'serverError',
  STORAGE_ERROR: 'storageError',
  NOT_FOUND: 'notFound',
  DATA_ERROR: 'dataError',
  OPERATION_CANCELLED: 'operationCancelled',
  PERMISSION_DENIED: 'permissionDenied',
  INVALID_PARAMETERS: 'invalidParameters',
  TIMEOUT: 'timeout',
  UNKNOWN: 'unknown'
};
```
<a name="atex-onecms-error-severities" />
#### atex.onecms.error.severities

The `atex.onecms.error.severities` object is an enumeration of error severities typically used in a OneCMS application. The `severity` property of a `atex.onecms.error.Error` object will typically be one of the enumeration values.

```javascript
atex.onecms.error.severities = {
  NORMAL: 'normal',
  CRITICAL: 'critical'
};
```

<a name="functionality-angularjs" />
### Exposed functionality - AngularJS

The AngularJS part of the OneCMS Code Library contains client-side services to communicate with server-side services in the Content REST API. The AngularJS services essentially wrap the core parts of the OneCMS Code Library with authentication logic to make sure that there is always a valid user.

##### General rules of thumb:

* One service implementation per server-side service.
* All service functions return Q-based promises.
* All service functions are wrapped with authentication logic.
* All services use the `ConfigurationService` service to read configuration.
* Unless specifically stated, all services use server-side endpoint locations relative to the configured **oneCMSUrl** property, which should point to a Content API REST web application containing both OneCMS services and a polopoly_fs file servlet.
* Returned promises are resolved with return data of the operation when the operation succeeds.
* Returned promises are rejected using atex.onecms.error.Error objects when the operation fails.

<a name="angularjs-events" />
### Events

The AngularJS part of the OneCMS Code Library may emit application-wide events to indicate that various things has happened or need to happen. Generally, it is up to the application (i.e. **NOT** the library) to decide what to do when these events are emitted. To subscribe to these events you need to do so using `$rootScope.$on()`.

<a name="event-onecms-authenticationRequest" />
#### Event onecms:authenticationRequest

This event is emitted when something requires an authenticated user to continue. Typically, this will be emitted by `atex.onecms.service.AuthenticationService` when it discovers that there is no logged-in user. This can happen in two scenarios, where one is that there is no user information stored at all. The other scenario is that there is information stored about the logged-in user, but in communication with the server it has responded that the token is invalid (`HTTP 401 Unauthorized`).

This event is broadcasted with one parameter, which is an object representing the current user (see `atex.onecms.service.AuthenticationService.getCurrentUser`).

##### Usage
```javascript
/**
 * This is how the OneCMS Starter Application uses onecms:authenticationRequest.
 */
$rootScope.$on("onecms:authenticationRequest", function(event, currentUser) {
  var dialog = AuthenticationDialog.create();

  dialog.whenAuthenticated().then(function() {
    AuthenticationService.confirmAuthenticationRequest();
  });
});
```

The call to `AuthenticationService.confirmAuthenticationRequest()` will re-run any actions that waited for authentication to complete.

<a name="event-onecms-authenticationSuccess" />
#### Event onecms:authenticationSuccess

This event is emitted when `AuthenticationService.confirmAuthenticationRequest()` is called.

##### Usage
```javascript
/**
 * This is how the OneCMS Starter Application uses onecms:authenticationSuccess.
 */
$rootScope.$on("onecms:authenticationSuccess", function(event) {
  AuthenticationService.getCurrentUser().then(function(user) {
    return ContentService.getContent({ id: user.userId, variant: 'onecms' });
  }).then(function(content) {
    $scope.currentUser = content.data.contentData.firstname;
  });
});
```

In this example, the OneCMS Starter Application uses the `onecms:authenticationSuccess` event to display the user's first name in the navigation bar.

<a name="module-atex-onecms-application" />
### Module atex.onecms.application

<a name="service-application" />
#### Service 'Application'

The `Application` AngularJS service represents the OneCMS application itself, but it's only use at this time is to be the entry point to current user credentials (stored in localStorage/cookies).

Most of the time, access to current crendentials information should go through the `AuthenticationService` service. Only use the `Application` service if you need raw access to what is currently stored.

```javascript
angular.module('atex.onecms.application').factory('Application',
[function() {

  var API = {
    /**
     * Resets all current browser user state, both in localStorage and cookies.
     *
     * Does not return anything.
     */
    reset: function() {
      // ...
    },

    /**
     * Sets browser cookies for 'userId' and 'sessionKey' based
     * on what is currently stored in browser localStorage.
     *
     * Does not return anything.
     */
    setCookies: function() {
      // ...
    }
  };

  /**
   * Getter and setter for the 'currentUser' property. The property object
   * should look like this:
   *
   * {
   *   userId: 'USERID',
   *   token: 'TOKEN',
   *   username: 'USERNAME'
   * }
   */
  Object.defineProperty(API, 'currentUser', {
    get: function() {
      // ...
    },

    set: function(newUser) {
      // ...
    }
  });

  return API;
}]);
```

##### Usage

```javascript
/**
 * Access the current user information through the Application object.
 */

angular.module('myApp', ['atex.onecms.application']).factory('MyService',
['Application', function(Application) {

  /**
   * The information found in Application.currentUser is normally populated by
   * AuthenticationService.login, so it should not be set manually. The following
   * is only for the purpose of this example...
   */
  Application.currentUser = {
    "userId": "USERID",
    "token": "TOKEN",
    "username": "USERNAME"
  };

  var currentUser = Application.currentUser;

  var userId = currentUser.userId;
  var token = currentUser.token;

  Application.setCookies();
  Application.reset();
}]);
```

<a name="service-applicationconfiguration" />
#### Service 'ApplicationConfiguration'

The `ApplicationConfiguration` AngularJS service handles application configuration. It provides methods for reading configuration by path. The configuration is always read from the server using an URL of 'configuration.json'. It is up to the project using the OneCMS Code Library to make sure there is such a resource available. The data available at the URL should be a JSON object.

```javascript
angular.module('atex.onecms.application').factory('ApplicationConfiguration',
[function() {
  return {

   /**
    * Reads the value for the given configuration path from the server-provided configuration.
    *
    * Argument 'configurationPath' - an array which corresponds to the
    * configuration path to read.
    *
    * Argument 'defaultValue' - the default value to use if a value cannot be found
    * at the specified 'configurationPath'. If no configuration can be found, and
    * this argument is omitted, undefined will be returned.
    *
    * Returns a Q-based promise.
    *
    * Resolves the return promise with the configuration data if the operation succeeds.
    * Rejects the return promise with an atex.onecms.error.Error object if the operation fails.
    */
    getConfigurationFor: function(configurationPath, defaultValue) {
      // ...
    }

  };
}]);
```

##### Usage

```javascript
/**
 * Read configuration for the 'myConfigKey' property provided
 * by the application server configuration.
 */

angular.module('myApp', ['atex.onecms.application']).factory('MyService',
['ApplicationConfiguration', function(ApplicationConfiguration) {

  /**
   * This assumes that there is a configuration value called 'myConfigKey'
   * in the configuration.json file:
   *
   * {
   *   "myConfigKey": "some configuration value",
   *
   *   "subObject": {
   *     "mySubConfigKey": 1000
   *   }
   * }
   */

  ApplicationConfiguration.getConfigurationFor(['myConfigKey'], "a default value").then(function(value) {
    // Do something with the configuration value...
  });

  ApplicationConfiguration.getConfigurationFor(['subObject', 'mySubConfigKey'], 6000).then(function(value) {
    // Do something with the configuration value...
  });
}]);
```

<a name="module-atex-onecms-authentication" />
### Module atex.onecms.authentication

<a name="service-authenticationdialog" />
#### Service 'AuthenticationDialog'

The `AuthenticationDialog` AngularJS service provides a way to show the login/authentication dialog.

Internally `AuthenticationDialog` uses `AuthenticationService.login()`, which in turn will use
`Application.currentUser` and `Application.setCookies()` to store information about the logged-in user.

Typically you would display an authentication dialog when the AngularJS event `onecms:authenticationRequest`
is broadcasted. When this event is fired, it means that something in your AngularJS application requires
an authenticated user.

```javascript
angular.module('atex.onecms.authentication').factory('AuthenticationDialog',
[function() {
  return {

    /**
     * Creates a new authentication dialog, using the provided parameters.
     *
     * Argument 'params' - A parameter object containing values used by the
     * authentication dialog. Merged with the default parameters, which
     * look like this:
     *
     * {
     *   autoDestroy: true,
     *   message: 'You are required to log in. Please enter your credentials:',
     *   username: undefined
     * }
     *
     * Property 'autoDestroy' determines whether the authentication dialog
     * should be automatically destroyed when the authentication process is
     * complete.
     *
     * Property 'message' contains the message shown in the authentication dialog.
     *
     * Property 'username' contains the username that should be pre-filled in
     * the authentication dialog. If this property is defined, the authentication
     * dialog will automatically be set to 'confirm' mode instead of login mode.
     *
     * Returns an object that looks like this:
     *
     * {
     *   whenAuthenticated: function() {
     *     // ...
     *   },
     *
     *   destroy: function() {
     *     // ...
     *   }
     * }
     *
     * The 'whenAuthenticated' property returns a Q-based promise that will be resolved
     * when the authentication process is completed.
     *
     * The 'destroy' property is a function that can be used to destroy the authentication
     * modal. Typically used with 'autoDestroy' property in the input parameters set to false.
     */
    create: function(params) {
      // ...
    },

  };
}]);
```

##### Usage

```javascript
/**
 * Create a new authentication dialog and force the user to authenticate.
 */
angular.module('myApp', ['atex.onecms.authentication']).factory('MyService',
['AuthenticationDialog', 'AuthenticationService', '$rootScope',
function(AuthenticationDialog, AuthenticationService, $rootScope) {

  // Upon onecms:authenticationRequest, display the authentication dialog
  $rootScope.$on("onecms:authenticationRequest", function(event, currentUser) {
    var dialog = AuthenticationDialog.create();

    dialog.whenAuthenticated().then(function() {
      // There will now be an authenticated user...

      AuthenticationService.getCurrentUser().then(function(currentUser) {
        // currentUser will contain the user that just authenticated...
      });
    });
  });
}]);
```

<a name="module-atex-onecms-service" />
### Module atex.onecms.service

<a name="service-authenticationservice" />
#### Service 'AuthenticationService'

The `AuthenticationService` AngularJS service handles everything concerning authentication and credentials. It provides methods for getting the current application user as well as common authentication functionality.

```javascript
angular.module('atex.onecms.service').factory('AuthenticationService',
[function() {
  return {

    /**
     * Confirms the current authentication request (if any). Any requests
     * currently queued will be re-executed with the same parameters and
     * context that they had the first time. Calling this method
     * triggers the event onecms:authenticationSuccess.
     *
     * Does not return anything.
     */
    confirmAuthenticationRequest: function() {
      // ...
    },

    /**
     * Cancels the current authentication request (if any). Any requests
     * currently queued will be rejected with an atex.onecms.error.Error object
     * where the 'type' property will be set to
     * atex.onecms.error.types.OPERATION_CANCELLED.
     *
     * Does not return anything.
     */
    cancelAuthenticationRequest: function() {
      // ...
    },

    /**
     * Runs the given function wrapped by authentication logic. If the function
     * results in an error with the 'type' property set to
     * atex.onecms.error.types.AUTHENTICATION_FAILED, the request is queued and an
     * authentication request application event is broadcasted.
     *
     * Argument 'fn' - the function to run wrapped in authentication logic.
     *
     * Returns a Q-based promise.
     *
     * Resolves the returned promise with the output from the provided function
     * if the operation succeeds.
     *
     * Rejects the returned promise with an atex.onecms.error.Error object if the
     * operation fails.
     */
    callWithAuthentication: function (fn) {
      // ...
    },

    /**
     * Returns information on the current user. If there is no current user,
     * a login authentication event will be broadcasted throughout the
     * application. For more information on how the returned object looks, see
     * Application.currentUser.
     *
     * Returns a Q-based promise.
     *
     * Resolves the returned promise with information on the current user if the
     * operation succeeds.
     *
     * Rejects the returned promise with an atex.onecms.error.Error object if the
     * operation fails.
     */
    getCurrentUser: function() {
      // ...
    },

    /**
     * Verifies that the token stored for the current user is still valid.
     *
     * Returns a Q-based promise.
     *
     * Resolves the returned promise with the same output as you would get from
     * AuthenticationService.login if the operation succeeds.
     *
     * Rejects the return promise with an atex.onecms.error.Error object if the
     * operation fails.
     */
    verifyLoggedIn: function() {
      // ...
    },

    /**
     * Authenticates against the server using the given username and password.
     * Also sets the authenticated user as the current user in the application.
     *
     * Argument 'username' - The username to authenticate with.
     * Argument 'password' - The password to authenticate with.
     *
     * Returns a Q-based promise.
     *
     * Resolves the returned promise with the user ID of the authenticated user
     * if the operation succeeds.
     *
     * Rejects the returned promise with an atex.onecms.error.Error object if the
     * operation fails.
     */
    login: function(username, password) {
      // ...
    }

  };
}]);
```

##### Configuration

The `AuthenticationService` reads the **oneCMSUrl** configuration parameter using the `ConfigurationService`. The server-side endpoint location of the Content API service used by this AngularJS service will then be relative to this location.

##### Usage

```javascript
/**
 * Use the AuthenticationService service to login.
 */

angular.module('myApp', ['atex.onecms.authentication']).factory('MyService',
['AuthenticationService', function(AuthenticationService) {
  AuthenticationService.login('edmund', 'edmund').then(function(tokenData) {
    // Handle the token data...
  });
}]);
```

<a name="service-contentservice" />
#### Service 'ContentService'

The `ContentService` AngularJS service handles reading and writing content to and from the Content REST API.

All methods exposed by `ContentService` are guarded by authentication wrappers (provided by the `AuthenticationService` service). If a request results in a `HTTP 401` response an AngularJS event, `onecms:authenticationRequest`, will be broadcasted throughout the AngularJS application. It is up to the application to listen and take action on this event. Typically, you would want to display the authentication dialog.

```javascript
angular.module('atex.onecms.service').factory('ContentService',
[function() {
  return {

    /**
     * Wraps atex.onecms.service.ContentService.get with an authentication
     * guard (provided by AuthenticationService.callWithAuthentication).
     *
     * See documentation for atex.onecms.service.ContentService.get.
     * See documentation for AuthenticationService.callWithAuthentication.
     */
    getContent: function(transferInfo) {
      // ...
    },

    /**
     * Wraps atex.onecms.service.ContentService.create with an authentication
     * guard (provided by AuthenticationService.callWithAuthentication).
     *
     * See documentation for atex.onecms.service.ContentService.create.
     * See documentation for AuthenticationService.callWithAuthentication.
     */
    createContent: function(transferInfo) {
      // ...
    },

    /**
     * Wraps atex.onecms.service.ContentService.update with an authentication
     * guard (provided by AuthenticationService.callWithAuthentication).
     *
     * See documentation for atex.onecms.service.ContentService.update.
     * See documentation for AuthenticationService.callWithAuthentication.
     */
    updateContent: function(transferInfo) {
      // ...
    }

  };
}]);
```

##### Configuration

The `ContentService` reads the **oneCMSUrl** configuration parameter using the `ConfigurationService`. The server-side endpoint location of the Content API service used by this AngularJS service will then be relative to this location.

##### Usage

```javascript
angular.module('myApp', ['atex.onecms.service']).factory('MyService',
['ContentService', function (ContentService) {
  var readTransferInfo = {
    id: '1.154',
    variant: 'VARIANT'
  };

  ContentService.getContent(readTransferInfo).then(function (responseInfo) {
    // ...
  });

  var createTransferInfo = {
    data: {
      contentData: {
        _type: 'TYPE',
        name: 'My Article'
      }
    }
  };

  ContentService.createContent(createTransferInfo).then(function (responseInfo) {
    // ...
  });

  var updateTransferInfo = {
    id: '1.154',

    data: {
      contentData: {
        _type: 'TYPE',
        name: 'My Article'
      }
    },

    version: 'contentData#policy:1.154:1398676898'
  };

  ContentService.updateContent(updateTransferInfo).then(function(responseInfo) {
    // ...
  });
}]);
```

<a name="service-searchservice"/>
#### Service 'SearchService'

The `SearchService` AngularJS service handles searching for content through the Content REST API.

The search method exposed by `SearchService` is guarded by an authentication wrapper (provided by the `AuthenticationService` service). If a request results in a `HTTP 401` response an AngularJS event, `onecms:authenticationRequest`, will be broadcasted throughout the AngularJS application. It is up to the application to listen and take action on this event. Typically, you would want to display the authentication dialog.

```javascript
angular.module('atex.onecms.service').factory('SearchService',
[function() {
  return {

    /**
     * Wraps atex.onecms.service.SearchService.search with an authentication
     * guard (provided by AuthenticationService.callWithAuthentication).
     *
     * See documentation for atex.onecms.service.SearchService.search.
     * See documentation for AuthenticationService.callWithAuthentication.
     */
    search: function(searchInfo) {
      // ...
    }

  };
}]);
```

##### Configuration

The `SearchService` reads the **oneCMSUrl** configuration parameter using the `ConfigurationService`. The server-side endpoint location of the Content API service used by this AngularJS service will then be relative to this location.

##### Usage

```javascript
angular.module('myApp', ['atex.onecms.service']).factory('MyService',
['SearchService', function(SearchService) {
  var searchInfo = {
    core: 'public',
    q: 'cars',
    rows: 10,
    variant: 'VARIANT'
  };

  SearchService.search(searchInfo).then(function(searchResponse) {
    var documents = searchResponse.data.response.docs;

    // Do something with the documents...
  }, function(error) {
    // The operation failed. Inspect the 'error' object (type atex.onecms.error.Error)
    // for more information.
  });
}
```

<a name="service-typeservice"/>
#### Service 'TypeService'

The `TypeService` AngularJS service handles type lookups in the Content REST API.

The get method exposed by `TypeService` is guarded by an authentication wrapper (provided by the `AuthenticationService` service). If a request results in a `HTTP 401` response an AngularJS event, `onecms:authenticationRequest`, will be broadcasted throughout the AngularJS application. It is up to the application to listen and take action on this event. Typically, you would want to display the authentication dialog.

```javascript
angular.module('atex.onecms.service').factory('TypeService',
[function() {
  return {

    /**
     * Wraps atex.onecms.service.TypeService.get with an authentication
     * guard (provided by AuthenticationService.callWithAuthentication).
     *
     * See documentation for atex.onecms.service.TypeService.get.
     * See documentation for AuthenticationService.callWithAuthentication.
     */
    get: function(transferInfo) {
      // ...
    }

  };
}]);
```

##### Configuration

The `TypeService` reads the **oneCMSUrl** configuration parameter using the `ConfigurationService`. The server-side endpoint location of the Content API service used by this AngularJS service will then be relative to this location.

##### Usage

```javascript
angular.module('myApp', ['atex.onecms.service']).factory('MyService',
['TypeService', function(TypeService) {
  var transferInfo = {
    id: 'java.lang.String'
  };

  TypeService.get(transferInfo).then(function(response) {
    // Do something with the response...
  }, function(error) {
    // The operation failed. Inspect the 'error' object (type atex.onecms.error.Error)
    // for more information.
  });
}
```

<a name="service-fileservice"/>
#### Service 'FileService'

The `FileService` AngularJS service handles file transfers to and from the file service in the Content REST API.

All methods exposed by `FileService` are guarded by authentication wrappers (provided by the `AuthenticationService` service). If a request results in a `HTTP 401` response an AngularJS event, `onecms:authenticationRequest`, will be broadcasted throughout the AngularJS application. It is up to the application to listen and take action on this event. Typically, you would want to display the authentication dialog.

```javascript
angular.module('atex.onecms.service').factory('FileService',
[function() {
  return {

   /**
    * Wraps atex.onecms.service.FileService.getFileAsBlob with an authentication
    * guard (provided by AuthenticationService.callWithAuthentication).
    *
    * See documentation for atex.onecms.service.FileService.getFileAsBlob.
    * See documentation for AuthenticationService.callWithAuthentication.
    */
    getFileAsBlob: function(transferInfo) {
      // ...
    },

   /**
    * Wraps atex.onecms.service.FileService.putFile with an authentication
    * guard (provided by AuthenticationService.callWithAuthentication).
    *
    * See documentation for atex.onecms.service.FileService.putFile.
    * See documentation for AuthenticationService.callWithAuthentication.
    */
    putFile: function(transferInfo) {
      // ...
    }

  };
}]);
```

##### Configuration

The `FileService` reads the **oneCMSUrl** configuration parameter using the `ConfigurationService`. The server-side endpoint location of the Content API service used by this AngularJS service will then be relative to this location.

##### Usage

```javascript
angular.module('myApp', ['atex.onecms.service']).factory('MyService',
['FileService', function(FileService) {
  var transferInfo = {
    uri: 'tmp://',
    data: new Blob(['abc']),

    authToken: '3000::dqou9H0JkApD2fpS6y4SOB1ooHex'
  };

  FileService.putFile(transferInfo).whenCompleted().then(function(response) {
    // Do something with the response...
  }, function(error) {
    // The operation failed. Inspect the 'error' object (type atex.onecms.error.Error)
    // for more information.
  });
}
```

<a name="changelog" />
### Changelog

#### 0.19.1

* Feature: The image editor widget now has scramble functionality.

#### 0.19.0

* Domain objects and widgets are now included in the OneCMS Code Library.
* Feature: Added FileService, both low-level and AngularJS APIs. The FileService can be used to both upload and download files from a Content API File service.
* Breaking change: All AngularJS services in the OneCMS Code Library are now reading a new configuration parameter: 'oneCMSUrl', which should point to a OneCMS web application.

#### 0.18.1

* UI Framework widgets now have access to the window object.

#### 0.18.0

* From this point on, the OneCMS Code Library is compatible with Polopoly 10.10. This means that it is incompatible with any earlier version than 10.10.

#### 0.17.0

* Breaking change: All (most) content in the OneCMS Code Library has switched namespace to atex.onecms.

#### 0.16.0

* Feature: The UI Framework can now handle widgets using names with several uppercase characters in a row.
* Breaking change: The UI Framework now expects Widgets to be stored on content prefixed by 'p.onecms.Widget-' and Domain Objects to be stored on content prefixed by 'p.onecms.DomainObject-'.

#### 0.15.0

* Breaking change: The UI Framework now has better facilities for representing types as domain object structures. The way we handle domain objects has been rebuilt from the ground up with much better support for basic customization and utilization.

#### 0.14.0

* Breaking change: UI Framework widgets now have a new way of registering themselves. Among other things, this allows widgets to specify dependencies upon 3rd party code.

#### 0.13.0

* Breaking change: As of 0.13.0, all files provided by this code library has new names. Please see [JAR file contents](#jar-file-contents).
* Feature: As of 0.13.0, the OneCMS Code Library includes the OneCMS UI Framework, but this might change in the future. For documentation on the OneCMS UI Framework, please see http://support.polopoly.com/confluence/display/PolopolyMaster/OneCMS+UI+Framework.
* Feature: Added TypeService, both low-level and AngularJS APIs to enable type lookup in the Data API.

#### 0.12.0

* Bug fix: Falsy configuration values (0, false, "") will no longer be interpreted as undefined.
* Bug fix: The promise returned by `atex.onecms.application.ApplicationConfigurationService.getConfigurationFor()` is now rejected if the configuration file cannot be parsed.
* Feature: You can now specify a default value when getting configuration from `atex.onecms.application.ApplicationConfigurationService`.

#### 0.11.1

* Update: Now testing with AngularJS version 1.2.6.

#### 0.11.0

* Feature: Use cache busting in Safari to avoid stale redirects.
* Feature: Added ported allSettled from q library.

#### 0.10.0

* Feature: SearchService, both low-level and AngularJS APIs to enable search through the Data API.

#### 0.9.0

* Initial release. Contains both low-level and AngularJS APIs for the Data API, authentication handling and configuration. Requires version 3.2.0 or greater of the OneCMS Theme.
