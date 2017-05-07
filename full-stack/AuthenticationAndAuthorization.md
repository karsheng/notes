# Authentication and Authorization
1. [Blog](http://lepture.com/en/2013/create-oauth-server) to create own OAuth Server with Flask
1. Authentication - verification of you are who you said you are.
1. Securing your web app:
	1. Strong passwords
	1. Strong encryption
	1. Secure communication
	1. Password storage
	1. Man-in-the-middle attacks
	1. 2 - factor authentication
	1. Password recovery
1. Authorization - do you have the right to the resource you're trying to access.
1. [Session hijacking](https://en.wikipedia.org/wiki/Session_hijacking) is the exploitation of a valid computer session to gain unauthorized access to info or services in a computer system. Basically, stealing someone's session cookie and pretend you're that person.
1. It is considered more secure to disallow direct root login and use `sudo` or `su` instead so someone has to authenticate.
1. OAuth is the most widely used standard for authorization.
1. OpenID Connect is a technology built on top of OAuth 2.0. Most big providers have adapted to use version 2. E.g. Facebook, Google, Amazon...
1. Using 3rd party - Pros:
	1. Outsource auth handling to OAuth providers.
	1. Easier to register users
	1. Less passwords for users to remember
1. Using 3rd party - Neutral:
	1. Users need to have a 3rd party account
1. Using 3rd party - Cons:
	1. Users don't trust your site - keep auth scopes minimal
	1. Offline apps - local auth may be a better option
	1. Can't have different auth requirements
1. [Google's Oauthplayground](https://developers.google.com/oauthplayground/) test API calls for the Google suite of products.
1. The term Auth Flow in web security refers to the way information is exchanged between a cient, server and OAuth provider to ensure secure communication across the internet.
1. OAuth allows for a flow that can happen on the client side. This means that all of the code to authenticate the user is initiated via JavaScript from the user's browser. This implementation is very useful for single-page browser-based web application.
1. OAuth also supports mobile authorization, such that mobile device apps can authenticate and gain access similarly to a browser.
1. Another possible and very popular flow is on the server side. Server side flow allows the server to obtain an access token to allow the server to make API requests on behalf of the user. The user has the option to set a timeout or revoke access to these tokens at any time. 
1. Each of these implementations has various pros and cons:
	1. Client side authentication is quick and easy, but a lot of trust is placed on the browser or mobile device and the server cannot make API calls to the OAuth provider on behalf of the user.
	1. Server side implementation gives more power to a server side application, but the server is now responsible for securely implementing session checking for its users and secure storage of these access tokens.
	1. More info on these [flows](https://aaronparecki.com/2012/07/29/2/oauth2-simplified).
1. Google+ uses a hybridized flow for logins that requires authentication to happen on the client, but allows the server to make API calls on behalf of the client.
1. Google+ Auth for server side apps:
	1. A user opts to log in with Google account, and is redirected to a Google Portal for granting access to your application.
	1. The user authorizes your app on client side using the JavaScript API client.
	1. The Google server sends a one-time code along with an access token back to the client. 
	1. The client then sends this same special one-time code back to your server. Then your server relays this one-time code back to the Google API server.
	1. In return, your server's given an access token from Google, enabling it to make its own API calls, which can be done even when the user is offline.
	1. This one-time code flow has a security advantage over a pure server side flow because Google provides token directly to your server, without any intermediaries. 
	1. Even if a one time code is discovered, it is extremely hard to use without your application's client secret.
	1. A client secret is a special code Google issues to verify your apps.
	1. [Using OAuth 2.0 to access Google APIs](https://developers.google.com/identity/protocols/OAuth2)
1. If your app is accepting requests from an authenticated user, you want to be sure that it's actually the user who came up with that request and it isn't someone tricking them into sending it.
1. Anti-forgery state tokens protect the security of your users by preventing anti-forgery request attacks.
1. The first step to protecting against this type of attack is by creating a unique session token that your client side code returns along side the Google generated authorization code.
1. In later steps, you will verify this unique session token with your server when a request is made to verify that the user is making the request and not a malicious script.
```HTML
<!-- GOOGLE PLUS SIGN IN BUTTON-->   
<div id="signInButton">
	<span class="g-signin"
	data-scope="openid email" <!-- this specifies what you want to get from Google -->
	data-clientid="YOUR_CLIENT_ID_GOES_HERE.apps.googleusercontent.com"
	data-redirecturi="postmessage"
	data-accesstype="offline" <!-- server can make request to the Google API server even if the user is not logged in -->
	data-cookiepolicy="single_host_origin" <!-- Use single_host_origin if our website only has a single host name, and no subdomains -->
	data-callback="signInCallback" <!-- callback method called after user clicked sign in -->
	data-approvalprompt="force"> <!-- user have to login each time user visit the login page, disable during production -->
	</span>
</div>
```
1. Sample callback function upon signin with Google+:
```JavaScript
  function signInCallback(authResult) {
    // if object contains parameter called code,
    //  authorization with Google API was successful
    if (authResult['code']) {
      // Hide the sign-in button now that the user is authorized,
      $('#signinButton').attr('style', 'display: none');
      // Send the one-time-use to the server, if the server
      // responds, write a 'login-successful' message to the web
      // page and then redirect back to the main restaurants page
      $.ajax({
        type: 'POST',
        url: '/gconnect?state={{STATE}}', // STATE variable is passed as an argumennt to verify against the cross-site reference forgery attack.
        processData: false, // do not want JQuery to process the response into a string
        contentType: 'application/octet-stream; charset=utf-8', // sending an arbitary binary stream of data. And the charset equal to utf-8 indicates that it is formatted using a universal character set called Unicode.
        data: authResult['code'],
        success: function(result) {
          if (result) {
            $('#result').html('Login Successful!<br>' + result + '<br>Redirecting....');
            setTimeout(function() {
              window.location.href = "/restaurant";
            }, 4000);
          } else if (authResult['error']){
            console.log('There was an error:' + authResult['error']);
          } else {
            $('#result').html('Failed to make a server-side call. Check your configuration and console.');
          }
        }


      })
    }
  }
```
}