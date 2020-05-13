# monday OAuth Example
sample server code for mdc oauth

## What it is

Server-side code you can use to authenticate against OAUth2 in monday.com.

![App preview](/assets/oauth_preview.gif)


## Prerequisites

This demo will create a server to go through the OAuth flow in monday.com. To set it up, first ensure you have a working node.js environment installed. We also recommend using `ngrok` to create a tunnel from a public URL to `localhost`.



## How to set it up and run the tool

1. Install the project dependencies by running `npm install`

2. Initiate an ngrok tunnel by running `ngrok http 3000`. This will create a tunnel from a public URL to `localhost:3000`

   ![ngrok screenshot](/assets/ngrok_screenshot.png)

3.   Register your monday app using [these instructions](https://gist.github.com/yuhgto/24c0e08fe11fddedf1c9b3b12a514dcf).

    a. Add your redirect URI to your monday app. If you're using ngrok, this should look something like `mytunnel1234.ngrok.io/oauth/callback`.

    b. Add the following scopes: `me:read, boards:read`

    c. Add your client ID, client secret, and redirect URI to the `index.js` file:
    ```
    var client_id = 'CLIENT_ID';
    var client_secret = 'CLIENT_SECRET';
    var redirect_uri = 'REDIRECT_URI';
    ```

4. Navigate to your app's public URL, and hit begin!

## Why it works

When a user navigates to the homepage URL, they are presented with a button to begin the authorization flow.

The app then redirects them to the monday OAuth URL, where they see the app's scopes and authorize it:
```
app.get('/start', function (req, res) {

  res.redirect('https://auth.monday.com/oauth2/authorize?' +
    querystring.stringify({
      client_id: client_id,
      redirect_uri: redirect_uri + '/oauth/callback',
      state: state,
      scopes: "me:read boards:read"
    }));

});
```

If the app is authorized, the user is sent to `/oauth/callback`. The app then sends a POST to the token URL to receive an access token and a refresh token:

```
app.get('/oauth/callback', function (req, res) {

  var authRequest = {
    url: 'https://auth.monday.com/oauth2/token',
    form: {
      redirect_uri: redirect_uri + "/oauth/callback",
      client_id: client_id,
      client_secret: client_secret,
      code: code,
    },
  };

  request.post(authRequest, function (error, response, body) { ... };
}
  ```
