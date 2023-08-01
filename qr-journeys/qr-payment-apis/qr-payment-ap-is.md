# QR Payment - APIs
### Create QR code

`POST /api/qr-service/client-api/v2/createQRCode HTTP/1.1 Content-Type: application/json { "accountNumber": "000000624", "currency": "VND", "amount": 1500000, "type": "INTERNAL_TRANSFER" } HTTP/1.1 200 OK Date: Wed, 05 May 2021 02:56:30 GMT Content-Type: application/json { "qrData" : "00020101021238570010A00000072701270006970403011300110123456780208QRIBFTTA530370454061800005802VN62340107NPS68690819thanh toan don hang63042E2E" }`

### Parse QR code

`POST /api/qr-service/client-api/v2/validate HTTP/1.1 Content-Type: application/json { "data" : "00020101021238570010A00000072701270006970403011300110123456780208QRIBFTTA530370454061800005802VN62340107NPS68690819thanh toan don hang63042E2E" } HTTP/1.1 200 OK Date: Wed, 05 May 2021 02:56:30 GMT Content-Type: application/json { "id": "A000000727", "accountNumber": "000000624", "currency": "VND", "amount": 1500000, "merchantName": "Viet Mart", "merchantCity": "Hanoi", "type": "INTERNAL_TRANSFER" }`

### Invalid QR code

`HTTP/1.1 400 Bad Request Date: Wed, 05 May 2021 02:56:30 GMT`

### Pay QR code

`POST /api/payment-order-service/client-api/v2/payment-orders HTTP/1.1 { "originatorAccount": { "identification": { "identification": "84b5af5b-96ff-4416-ba1d-834a8e7726df", "schemeName": "ID" } }, "requestedExecutionDate": "2021-05-05", "paymentType": "INTERNAL_TRANSFER", "transferTransactionInformation": { "instructedAmount": { "amount": "1500000", "currencyCode": "VND" }, "counterparty": { "name": "NguyetTran Shop" }, "counterpartyAccount": { "identification": { "identification": "000000624", "schemeName": "BBAN" } } } }`