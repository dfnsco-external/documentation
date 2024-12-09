# Passkey Settings - Migration guide

## **TLDR;**

We’re gonna remove the `rpId` and `origin` properties from Dfns “Applications”, and create a separate “Passkey Settings” page to configure passkey origins. As a result, an upcoming breaking change in the API is introduced, and require our customers to do a minor update in their client-side code (in web apps or mobile apps). The change is about specifying the relying party ID explicitly in those apps.

***



## **First, some context**

#### On Passkeys & Relying Party

With Dfns, users can use [Passkeys](https://fidoalliance.org/passkeys/) to authenticate with our systems. A passkey – also known as “Fido2 credential” or “WebAuthn credential” – is a cryptographic key created and stored securely on a user’s device: a laptop, a phone, an external device like Yubikey, it can also be stored on the cloud, on password manager etc. Passkeys offer a more secure, yet better UX for user authentication than good-old passwords. From the user’s perspective, it often takes the form of a simple biometric check like face ID / fingerprint.

By design – for security reasons – any passkey is scoped to a “Relying Party ID”, specified during creation of the passkey. The Relying Party ID (`rpID`, for short) is essentially a domain owned by you – the organisation – under which your web app is hosted, or to which your mobile app is tied (eg. `acme.org`, `tesla.com`, etc.). This `rpId` defines on which app – or set of apps – a given passkey will be usable by a user.

As an example, if you want a user to use the same passkey in:

* a web app `https://foo.acme.com`
* a web app `https://bar.acme.com`
* an IOS mobile app
* an Android app

then the `rpId` specified during creation of the passkey must be the root domain “`acme.com`“

{% hint style="info" %}
Note: mobile apps must be “associated with” the domain `acme.com`. IOS and Android setup is slightly different, but involves hosting a .well-known file on your domain, to “prove domain is yours and associate the mobile app“, eg. `https://acme.com/.well-known/assetlinks.json` for [Android](https://developer.android.com/identity/sign-in/credential-manager), or `https://acme.com/.well-known/apple-app-site-association` for [IOS](https://developer.apple.com/documentation/xcode/supporting-associated-domains).
{% endhint %}

#### On Dfns Applications

Every request to Dfns API takes an Application ID in the `x-dfns-appid` header. [Applications](https://docs.dfns.co/d/api-docs/authentication/application-management) were originally designed to have a dual function:

* First function was to be an additional permission layer on top of the User’s permission. Any API request is checked against the intersection of both the Application permissions & the User permissions. That would allow custom setups when an organisation wants to setup different applications with different levels of permissions
* Second function was to specify which domain (`rpID`) & `origin` was the Application expected to be used in, and therefore, validate that passkeys used in that application match the `rpId` specified on the Application used, and are indeed used from the specified `origin`.

So, with that design, the app ID you use in the Dfns API request also determines which passkey is allowed to be used.

***



## **Motivation for the change**

After gathering our customer’s feedback, it turns out Dfns Applications are more a source of confusion and complication than anything else, especially newcomers.

The main reason is that the relationship between the Application and the Passkey is not obvious. We – Dfns – thought that tying those things together would hide away the complexity from users and make it more seemless. Turns out that people were more confused as to what those things meant, and the relationship between them, and in turn, how to set it up properly.

Also, since we offer different kind of credentials to authenticate (like raw asymmetric keys), but the `rpID` / `origin` setup on Applications is only relevant in the context of a passkey credential, that could add to the confusion.

Another reason is that the setup was a bit cumbersome if you had several several web-apps + mobile apps to setup, as you needed to create several Dfns Applications, and use the right Application ID from the right place in order for everything to work out properly alongside with Passkeys.

We believe that the domain (Relying Party ID) that you choose to create your passkeys in your client apps, should be in your control, client-side. The only reason Dfns needs to know about it, is to “whitelist” these domains you wanna use.

**So we decided to update that design a bit, and extract the** `rpId` **and** `origin` **properties outside of Dfns Applications, and instead introduce separate “Passkey Settings” accessible through Dfns dashboard.**

Only future will tell if we go a step further, and also decide to revise our Application model + the required Application ID passed in any Dfns API request (as header). But regardless, this current change was a necessary first step.

***



## **How to migrate ?**

To whitelist a new domain (`rpId`) where your users need to create/user passkeys, you can now go to Dfns dashboard, into `Settings > Passkeys`, and whitelist the domain from there:

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

When whitelisting a Relying Party ID, every origin in the “realm” of this `rpID` (all subdomains + associated mobile apps) will be allowed for passkeys to be used. That’s what the wildcard "`*`" means in the screenshot above.

In case you need to specifically whitelist only a specific subset of origins from which a passkey should be used – _for most use cases, this shouldn’t be necessary_ – you can specify “only specific origins” in the whitelisting form, and specify them:

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

All `rpIds` previously specified in your existing Dfns Applications will already be pre-filled in these new Passkey Settings, with `origins = *`.

When you create a new Dfns Application from our Dashboard, you won’t be able to specify `rpId` + `origin` anymore. Instead, if your goal is only to whitelist a new domain, you won’t necessarily need to create a new Application, but rather you’ll need to add the new `rpId` in the “Passkey Settings” page.

If you still create a new Application (so without `rpId` attached), your client-side code needs to account for that (see how below).

During a transition period of N months, if you keep using Applications created before this change (which still have a `rpId` attached), the API won’t introduce a breaking change, as the App `rpId` will still be included in every challenge endpoint payloads (eg [Create Delegated Registration Challenge](https://docs.dfns.co/d/api-docs/authentication/delegated-auth/delegatedregistration) or [Create User Action Challenge](https://docs.dfns.co/d/api-docs/authentication/user-action-signing/inituseractionsigning)).

After the transition period, **a breaking change will be introduced in the API**: `rpId` + `origin` properties will be entirely removed from all Applications (even old ones). As a result,`rpID` will not be included in challenge payloads anymore. So far, your client side code may have relied on this `rpID` being returned in the challenge payload, to pass it along to the WebAuthn client during passkey creation / usage.

To account for this upcoming breaking change, **your client-side app code must stop relying on the** `rpId` **returned in the challenge payloads**. Instead, the relying party rather needs to be specify explicitly in your client-side code, during passkey creation / usage.

For people using our SDKs, it basically means upgrading the SDK, and make one change (see below).

#### ➡️ For web apps:

If you used our [Typescript SDK](https://github.com/dfns/dfns-sdk-ts) in a web app, you must upgrade to the newest version of `@dfns/sdk` and `@dfns/sdk-browser`, where `WebauthnSigner` was updated to take in the relying party in the constructor:

```typescript
// before
const signer = new WebAuthnSigner()

// after
const signer = new WebAuthnSigner({
  relyingParty: { id: 'acme.com', name: 'Acme' }
})
```

If you have a web app and are interacting with WebAuthn yourself (without using our Typescript SDK), you must provide the relying party in the `navigator.credentials.create(...)` and `navigator.credentials.get(...)` calls (if you didn’t already). Eg

```typescript
// passkey creation
const cred = await navigator.credentials.create({
  publicKey: {
    ...,
    rp: { id: 'acme.com', name: 'Acme' }
  }
})

// passkey usage
const cred = await navigator.credentials.get({
  publicKey: {
    ...,
    rpId: 'acme.com'
  }
})
```

#### ➡️ For React Native Apps:

If you used our [Typescript SDK](https://github.com/dfns/dfns-sdk-ts) in a react native app, you must upgrade to the newest version of `@dfns/sdk` and `@dfns/sdk-react-native` where `PasskeysSigner` was updated to take in the relying party in the constructor:

```typescript
// before
const signer = new PasskeySigner()

// after
const signer = new PasskeySigner({
  relyingParty: { id: 'acme.com', name: 'Acme' }
})
```

#### ➡️ For IOS Apps:

If you used the [Swift SDK](https://github.com/dfns/dfns-sdk-swift) as an example / source in your IOS app, you must get the latest changes to it, and then:

```swift
// before
let signer = PasskeysSigner()

// after
let signer = PasskeysSigner(relyingPartyId: "acme.com")
```

#### ➡️ For Android Apps:

Same principle than above: your client-side apps code must not rely on the \*\*\*\*`rpId` \*\*\*\*returned in the challenge payloads, and you rather need to specify the relying party explicitly, during passkey creation / usage.



**Last point**: from now on, don’t necessarily need to use several Dfns Application IDs in your code, you can always use the same App ID if you want to (if all Dfns Apps you use have the same set of permissions).

***



## **What’s Next ?**

Hopefully this change makes setting up passkeys with Dfns more transparent and less confusing, as well as making it easier to setup passkeys for cross-subdomain usage (`rpId` needs to be common root domain).

You might be wondering if later it will would be possible to use passkeys cross-domain. Meaning, not just cross-subdomains of the same root domain (this is already possible), but across completely different root domains. Eg. use the same passkey both in `acme.com` and `tesla.xyz`. This matter, called “_Related Origins_” is being discussed in WebAuthn specification, and as the community converges to a new spec to allow this, it will progressively be supported in all platforms.

When Related Origins becomes widely implemented in major platforms, from Dfns perspective, no additional changes will be needed for you to support cross-root-domain passkeys. You’ll just need to follow the new spec to associate those related domains together, and then whitelist a new domain in Dfns.

More on “Related Origins” on the following links:

* [https://passkeys.dev/docs/advanced/related-origins/](https://passkeys.dev/docs/advanced/related-origins/)
* [https://github.com/w3c/webauthn/wiki/Explainer:-Related-origin-requests](https://github.com/w3c/webauthn/wiki/Explainer:-Related-origin-requests)
* [https://chromestatus.com/feature/4635336177352704](https://chromestatus.com/feature/4635336177352704)

You can also read more on the official WebAuthn spec [here](https://www.w3.org/TR/webauthn-2/).
