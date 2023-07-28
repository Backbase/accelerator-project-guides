# Get the Facet-ID for a PlayStore signed app
### Problem

If the client decides to let PlayStore Console manage the signing of his app, the final app provided by PlayStore will have a different signature than the signed app uploaded to PlayStore Console (manually or using a pipeline).

In that case, the device enrollment flow will fail on the facet-id validation step, and that’s because the corresponding **Facet-ID** for that _PlayStore signed app_ has not been registered yet server side.

As we know, we can get the facet-id by running the following line of code: `Log.i(TAG, "TrustedFacetID: " + FidoUafFacetUtils.getFacetID(context))`, but, we’re not able to see the logs in an app signed by PlayStore.

### Solution

We can get the **Facet-ID** for a _PlayStore signed app_ given his **SHA-1** certificate fingerprint.

#### Steps:

The first step is getting the SHA-1 certificate fingerprint, there’s two ways to do it:

1.  From Google Play Console:
    
    1.  Go to _**Setup / App**_ _**integrity**_ menu and select _**App signing**_ tab
        
    2.  The SHA-1 value must be available in the _**App signing key certificate**_ section
        
2.  From the .apk file:
    
    1.  To download the signed apk from Google Play Console, go to **Production > Downloads** Tab and click the **Signed, universal APK** link.
        
    2.  Change the extension of the .apk file to .zip and unzip it
        
    3.  Run the following command: `keytool -printcert -file META-INF/GOOGPLAY.RSA` to get the corresponding SHA-1 certificate fingerprint.
        

now that we got the SHA-1, we just need to run the following command in order to get the corresponding facet-id:

`echo THE_SHA1_VALUE | xxd -r -p | base64 | tr '/+' '_-' | tr -d '='`

and that’s it, now we can ask to the client to register that Facet-ID.