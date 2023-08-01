# QR Implementation notes - Android
With the implementation of the QR scanning, we have used the Google ML Kit vision API to scan and parse the QR codes. 

With ML Kit's barcode scanning API, the app can read data encoded using most standard barcode formats. Barcode scanning happens on the device, and doesn't require a network connection.

Because ML Kit can automatically recognize and parse this data, the app can respond intelligently when a user scans a barcode.

**Links**:

[![](./qr-implementation-notes-android-0.png)Barcode scanning  |  ML Kit  |  Google for Developers](https://developers.google.com/ml-kit/vision/barcode-scanning)

[![](./qr-implementation-notes-android-1.png)Scan barcodes with ML Kit on Android  |  Google for Developers](https://developers.google.com/ml-kit/vision/barcode-scanning/android)

### Key changes:

ML Kit's barcode scanning API, continuously reads the camera frame and tries to detect the barcode/QR code, so, multiple times we received the QR data.

To restrict that, we have implemented the listener for the barcode scanning. Once this listener is triggered, we stop the barcode scanning processor to process the camera frames.

We re-initialized the barcode processor again, in case of any edge case scenarios, i.e. wrong/invalid barcode, to scan the barcode again.

### How to use the QR Payment Journey:

1.  Import the “QRPaymentJourney”, “qr-payment-client” and “qr-payment-usecase” modules in the project or add as .aar files in project’s libs folder
    
2.  Modify the navigation graph to add QRPayment journey :
    
```
    <fragment
      android:id="@+id/qrPaymentJourney"   
      android:name="com.backbase.android.retail.journey.qr.payments.ui.QRPaymentJourney"
      android:label="Payment Journey"
      tools:layout="@layout/qr_payment_journey" />
```
    
3.  Add koin dependencies:
    
```
single {
        Moshi.Builder()
            .add(bigDecimalAdapter)
            .add(base64Adapter)
            .add(dateAdapter)
            .add(KotlinJsonAdapterFactory()) // add kotlin adapter for moshi 
            .add(
                /**
                 * Some dates come in a format that is not an ISO standard from the backend. That format describes
                 * timezones as [+-]HHmm$.
                 *
                 * This workaround converts the string's timezone to [+-]HH:mm$. When the backend fixes this issue,
                 * the current workaround will not break the app, though it's advisable to remove it.
                 *
                 * The bug this code works around is known to be present only in the following spec: message-client-api-v4.0.7.yaml
                 */
                object {
                    @FromJson
                    fun fromJson(string: String) =
                        OffsetDateTime.parse(string.replace(Regex("(?<=[+-])(\\d{2})(\\d{2})$"), "$1:$2"))

                    @ToJson
                    fun toJson(value: OffsetDateTime) = value.toString()
                }
            )
            .build()
    }
 //QR Scan and Payment journey
    scope<QRPaymentJourneyScope> {
        scoped { //Scan QR
            QRPaymentJourneyConfiguration {}
        }
        scoped { //Scan QR
            val serverUri = URI("${apiRoot()}/qr-service-dis/client-api")
            QRScannerValidationClient(
                context = get<Application>(),
                moshi = get(),
                parser = get(),
                serverUri = serverUri,
                provider = get(),
                backbase = get(),
            )
        }
        scoped<QRPaymentUseCase> { //Scan QR
            QRScannerUseCaseImpl(get())
        }
        scoped {//Generate QR
            val serverUri = URI("${apiRoot()}/qr-service-dis/client-api")
            QRCodeGeneratorClient(
                context = get<Application>(),
                moshi = get(),
                parser = get(),
                serverUri = serverUri,
                provider = get(),
                backbase = get(),
            )
        }
        scoped { //Generate QR
            QRCodeGeneratorScreenConfiguration {  }
        }
        scoped { ProductsClient(get<DBSDataProvider>(), get(COMMON_API_ROOT_QUALIFIER)) } //Generate QR
        scoped<AccountsUseCase> { //Generate QR
            AccountsUseCaseImpl(productsClient = get())
        }
        scoped<QRGeneratorUseCase> { //Generate QR
            QRGeneratorUseCaseImpl(get())
        }
    }
```
    
4.  Implement router definition in your Activity koin module:
    
   ```
   factory {
       val navController = findNavController()
       object : QRPaymentRouter {
          override fun onCompleteQRValidation(productData: Product) {
              //We can navigate to payment journey here           
          }
        }
    } bind QRPaymentRouter::class
   ```