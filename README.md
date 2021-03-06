# Discourse SSO
Easy, configurable Discourse SSO: **GET api/auth/login -> recieve a JWT with user data**. Allows using Discord as your SSO provider to authenticate users into your own websites.

Based on the [official implementation](https://meta.discourse.org/t/using-discourse-as-a-sso-provider/32974), written in ASP.NET Core, but you needn't touch the code, everything is setup using [configuration](#configuration). This means you can use this regardless of your tech stack.

Using this will allow you to share your discourse userbase with your websites!

# Usage
1. [Build](https://docs.microsoft.com/en-us/dotnet/articles/core/deploying/) the project for your target OS (probably ubuntu)
2. Add your [configuration](#configuration) to `appsettings.json`
3. In your Discourse app, go to settings -> login -> and set `enable sso provider` to true, also enter the `sso secret`
4. That's it! Perform `GET api/auth/login` to get a [JWT contining user data](#token)! (the user will be prompted to log in if not already logged in)

Ideally your JS client would make that request then store the JWT and use it in the authorization header when querying your APIs, once the JWT experies it would make that request again to get another JWT and so on.

# Configuration
The configuration is located in the file `appsettings.json`, resembling:
```
{
  "DiscourseSso": {
    "SsoSecret": "Xmp4wB6aMULmujXd", // sso secret you setup in Discourse settings
    "DiscourseRootUrl": "http://community.example.com" // your discourse site URL without the trailing backslash at end
  },
  "Jwt": {
    "SecretKey": "Xmp4wB6aMULmujXd", // key used to sign the JWT (the one your APIs will use to validate it)
    "Issuer": "DiscourseSso", // "iss" claim in JWT
    "Expiry": "", // "exp" claim in JWT, if empty will default to 30 days
    "Audience": "DefaultAud" // "aud" claim in JWT
  }
}
```

# Token
The JWT that you'll receive will look like this:
```
{
  "sub": "1",
  "iss": "DiscourseSso",
  "iat": 1485393544,
  "exp": 1487985544,
  "aud": "DefaultAud",
  "name": "UserName+LastName",
  "username": "user",
  "email": "user@example.com",
  "external_id": "1",
  "admin": "true",
  "moderator": "false"
}
```
The `sub` claim is equivalent to `external_id`.
