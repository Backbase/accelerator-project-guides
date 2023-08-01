# Research of QR code payment in APAC
This page shares the information about QR code payment features in APAC countries - Vietnam, Philippines, Korea and Japan.

|     |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- |
|     | **Vietnam** | **Philippines** | **Korea** | **Japan(1)** | **Japan(2)** |
| **Basic info** |     |     |     |     |     |
| Bank/Company | * U Bank | * BDO | * KakaoPay<br>    <br>* NAVERPay | * BankPay | * PayPay<br>    <br>* dBarai |
| App type | **Bank App** | **Bank App** | eWallet App | **Bank Consortium App** | eWallet App |
| Code type | * QR code | * QR code | * QR code<br>    <br>* Barcode | * QR code<br>    <br>* Barcode | * QR code<br>    <br>* Barcode |
| Standard QR code guideline<br><br>*link is just for reference | Published | Published | Pilot | Published |     |
| Available mode(*1) | * MPM<br>    <br>* CPM | * MPM<br>    <br>* CPM | * CPM | * MPM(QR)<br>    <br>* CPM(QR, Barcode) | * MPM(QR)<br>    <br>* CPM(QR, Barcode) |
| Fund source | * Bank A/c<br>    <br><br>*map only to one account | * Bank A/c<br>    <br><br>*selectable from multiple accounts | * Prepaid card<br>    <br>* Credit Card<br>    <br>* Earned points | * Bank A/c<br>    <br><br>*register up to 8 A/c from member banks | * Bank A/c<br>    <br>* Prepaid card<br>    <br>* Credit Card<br>    <br>* Earned points |
| **Function Info** |     |     |     |     |     |
| Top up/charge | Not necessary | Not necessary | Required if the source is Prepaid Card | Not necessary | Required if the source is Prepaid Card |
| Share | * SNS<br>    <br>* SMS<br>    <br>* eMail<br>    <br>* Upload from storage<br>    <br>* Download from storage<br>    <br><br>*for sharing, the generated code is encrypted and require a camera to decrypt the code | * SNS<br>    <br>* SMS<br>    <br>* eMail<br>    <br>* Upload from storage<br>    <br>* Download from storage | * cannot share the code itself<br>    <br>* Just show or read the code | * cannot share the code itself<br>    <br>* Just show or read the code | * cannot share the code itself<br>    <br>* Just show or read the code |
| ATM<br><br>*basically this feature depends on ATM spec | n/a | * Cash-in<br>    <br>* Cash-out<br>    <br><br>*not in MVP | n/a | n/a | * Cash-in<br>    <br>* Cash-out |
| Edit Amount | * Editable for MPM<br>    <br>* Editable for CPM<br>    <br><br>*info populated in QR code is up to which payment GW is used to transfer money | * Manual input for MPM | * Not editable for CPM | * Manual input for MPM<br>    <br>* Not editable for CPM | * Manual input for MPM<br>    <br>* Not editable for CPM |
| Security<br><br>(Txn Signing) | Authentication is required after a summary page<br><br>-PIN<br><br>-Biometric | Authentication is required after a summary page<br><br>-PIN<br><br>-Biometric | Authentication is required to generate the code<br><br>-PIN<br><br>-Biometric | Authentication is required to generate the code<br><br>-PIN<br><br>-Biometric | Authentication is required after a summary page<br><br>-PIN |
| Point/Coupon<br><br>Program | n/a | n/a | * Earn points based on how much transacted thru the app<br>    <br>* Earned points can be used for payment | n/a | * Earn points based on how much transacted thru the app<br>    <br>* Earned points can be used for payment |

\*1 Terminology related to QR code payment mode is referred from **EMVCo.,LLC.**, which is a body to define the international settlement protocol (Ref. [https://www.emvco.com/emv-technologies/qrcodes/](https://www.emvco.com/emv-technologies/qrcodes/) )

MPM = Merchant Presented Mode (=User reads the code)

CPM = Consumer Presented Mode (=User generates the code)