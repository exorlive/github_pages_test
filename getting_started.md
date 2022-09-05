{% include menu.md %}

# Getting started

Authentication
Some of our services requires you to be authenticated with an OAuth 2.0 token (not required for the JWT signed link). The Oauth2 process requires you to get in [touch with us](/support.md) directly so we can add your domain to our authorization server and grant you:

- A client key
- A client secret
- A redirect uri
- An admin user id

These are used for our OAuth 2 flow. Read more about this in our [authentication](/authentication.md) section.

After you have been in contact with us you need to decide how to you wish to integrate ExorLive into your aplication. To figure this out, we have outlined a few cases in the sidebar menu and they should hopefully guide you in the right direction.

## ExorLive domains

Depending on how you are planning to use our API you might have to whitelist our domains.

- auth.exorlive.com - Our authentication server in charge of OAuth2.0.

- www.exorlive.com - Our company's main webpage.

- exorlive.com/ - Appdomain / Base url for all methods.

- exorlive.com/m/ - ExorLive Go.

- exorlive.com/app/ - The main ExorLive application, for instructors.