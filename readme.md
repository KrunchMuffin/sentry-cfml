# Sentry Client for ColdFusion

sentry-cfml2 is based on the fork sentry-cfml client developed 
by GiancarloGomez (https://github.com/GiancarloGomez/sentry-cfml)
which it was based off of the original raven-cfml client 
developed by jmacul2 (https://github.com/jmacul2/raven-cfml)

The script from which this was forked would not work for me as there seemed 
to be some major changes to Sentry's SDK.
I have updated to reflect these changes and also added Ben Nadel's JSON Serializer 
as the original one included was not robust enough.
https://github.com/bennadel/JsonSerializer.cfc
I also made some performance improvements, removed some unecessary code and a new level property 
with a default so we don't need to throw a hard error. 
Also removed the cgiVars as an option to pass in something else. The script has a reliance on the CGI vars being passed in and deviating from that would break the script. 
I have also implemented some basic error logging and added the 429 Too Many Requests check.

sentry-cfml2 is a CFML client for [Sentry](<https://sentry.io/welcome/>) 
and it has been updated to work with Sentry's Protocol @ version 7.

sentry-cfml2 is for use with ColdFusion 2016, 2018. Testing on earlier versions of ColdFusion and Lucee has not been done.

Sentry SDK Documentation
https://docs.sentry.io/clientdev/

## Installation
To install simply clone or download the sentry.cfc and customJsonSerializer.cfc file and place it anywhere in your
project. By default both files should be placed in the same directory.

## Instantiating as a Singleton
sentry-cfml2 can be instantiated each time you call it or it can
also live as a Singleton in your Application scope.

```javascript
function onApplicationStart(){

    application.sentry = new path.to.sentry(
        release     : "release-number-of-your-application",
        environment : "production|staging|etc",
        publicKey   : "your-public-key",
        privateKey  : "your-private-key",
        projectID   : "your-project-id"
    );

    return true;
}
```

## Using in your Application
Add to the onError() function to use for application wide errors.
 ```javascript
function onError(
    exception,
    eventName
){
    application.sentry.captureException(
        exception : arguments.exception
    );
}
```

## Usage
It is recommended that you review the [Sentry SDK Docs](https://docs.sentry.io/clientdev/attributes/) to understand the attributes and Interfaces that are supported.

## Examples
The following are examples on how to send messages and errors to Sentry. The examples are based on the singleton instance.

### Passing Messages
An information Message using a thread to post to Sentry
including data that is passed into the [User Interface](https://docs.sentry.io/clientdev/interfaces/user/)
```javascript

    application.sentry.captureMessage(
        message     : "This is just info",
        level       : "info",
        useThread   : true,
        userInfo    : {
            id          : 100,
            email       : "john.doe@test.com",
            type        : "administrator",
            username    : "john",
            ip_address  : cgi.remote_addr
        }
    );

```

Other level types allowed by Sentry
```javascript

    application.sentry.captureMessage(
        message :"This is a fatal message",
        level   :"fatal"
    );

    application.sentry.captureMessage(
        message :"This is an error message",
        level   :"error"
    );

    application.sentry.captureMessage(
        message :"This is a warning message",
        level   :"warning"
    );


    application.sentry.captureMessage(
        message :"This is a debug message",
        level   :"debug"
    );

```

### Capturing Errors
To capture an error you simply use the ``captureExeption`` function. Capturing an exception allows
for more options than just posting a message. Review the argument hints on the CFC for more information.
```javascript

    application.sentry.captureException(
        exception                   : e,
        level                       : "error",
        oneLineStackTrace           : true,
        showJavaStackTrace          : true,
        removeTabsOnJavaStackTrace  : false,
        additionalData              : {
            session : session
        },
        useThread                   : true,
        userInfo                    : {}
    );

```


