# QR - Mobile journey and configuration
### Android

#### Journey dependency

QR Journey artifacts available under the below repo:  
  
[https://github.com/baas-devops-cse/apac-cse-android-retail-app/packages](https://github.com/baas-devops-cse/apac-cse-android-retail-app/packages "https://github.com/baas-devops-cse/apac-cse-android-retail-app/packages")

Modify the project level build.gradle to add the GitHub maven dependencies:

```
allprojects {
    repositories {
        //google, jcenter etc.. here
       ...
        maven {
            name = "GithubPackages"
            url = uri("https://maven.pkg.github.com/baas-devops-cse/apac-cse-android-retail-qr-payment-journey")
            credentials {
                username = System.getenv("GPR_USER") ?: GPR_USER //Add this in global gradle.properties file
                password = System.getenv("GPR_KEY") ?: GPR_API_KEY //Add this in global gradle.properties file
            }
        }
    }
}
```

Here username is the Github UserID and password is the [GitHub Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-token "https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-token"). Add this in your global gradle.properties file or as system variable.

Modify the app module level build.gradle file to add the QR journey artifacts dependencies:

```
implementation 'com.backbase.android.retail.journey:qr-payment-journey:x.x.x'
implementation 'com.backbase.android.clients:qr-payment-client:x.x.x'
implementation 'com.backbase.android.retail.journey:qr-payment-usecase:x.x.x'
```

#### Journey configuration

This journey has the following configuration:

*   journeyTitle: Text to display on top of the QR Scan screen. Defaults to “Scan to Pay”.
    
*   description: Text to display on the QR Scan screen below the title bar. Default to “Scan QR code within the frame”.
    
*   leftActionButtonLabel: The title of the left action button to get the QR Image from the system gallery. Defaults to "Gallery".
    
*   leftActionButtonIcon: The icon to display on the left action button.
    
*   rightActionButtonLabel: The title of the right action button to generate and share the QR Image. Defaults to "My QR".
    
*   rightActionButtonIcon: The icon to display on the right action button.
    
*   supportedQRText: The title of the supported QR section. Default to “Supported QR codes:“.
    
*   supportedQRCodes: List of icons for supported QR’s. Default has 4 supported QR’s “Backbase, VNPay, napas247, VietQR“.
    
*   isFlashEnabled: Enable or disable the Camera flash button. Default value is true.
    
*   navigationAction: Navigation graphs action to redirect after the QR code is parsing successfully.
    

#### Entry and Exit point:

Navigate to the QRPayment journey using the Navigation Components library. Example:

`findNavController().navigate( R.id.action_anyScreen_to_qrPaymentJourney )`

The QRPayment journey provides an exit point for when the QR code is scan validated successfully. The QRPaymentRouter has the following method:

*   onCompleteQRValidation(productData: Product): Called when the QR is successfully validated.
    

To implement the exit point, create and register an instance of the QRPaymentRouter:

```
findNavController().navigate(
    R.id.action_anyScreen_to_qrPaymentJourney
)
```

#### Use cases

The following use cases are available for the QRPaymment journey out of the box:

*   QRValidationUseCaseImpl: The out-of-box implementation of the QRPaymentUseCase that uses to validate the scanned QR code and provided the Account information.
    

Below is the complete example of the QRPayment journey configuration:

```
factory {
      val navController = findNavController()
      object : QRPaymentRouter {//You can also create separete class to implement the router and call instance here
          override fun onCompleteQRValidation(productData: Product) {
                  //from here you can navigate to payment journey
          }
      }
} bind QRPaymentRouter::class
```

### IOS

#### Journey dependency

#### Journey configuration