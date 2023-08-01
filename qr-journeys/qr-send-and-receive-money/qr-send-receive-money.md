# QR - Send & Receive Money
## Background

QR codes transactions are widely used in many countries across ASIA, hence for P2P transfers, QR codes are used to empower quick transfers.

Our vision is to create a journey for P2P transfers to add value to consumers to remit funds swiftly.

## Retail Banking Mobile app - APAC Edition

QR - Send money

QR - Receive money

### User Story

#### Send Money

As a retail banking customer

I want to scan a QR to transfer

So that I can send money to my friends quickly

#### Receive Money

As a retail banking customer

I want to share my personal QR

So that I can receive money from my friends

### User Story Mapping

Send Money - [![](./qr-send-receive-money-0.png)APAC - Ideation](https://miro.com/app/board/o9J_lnZwaOo=/?moveToWidget=3458764516235904439&cot=14)

Receive Money - [![](./qr-send-receive-money-1.png)APAC - Ideation](https://miro.com/app/board/o9J_lnZwaOo=/?moveToWidget=3458764516236019066&cot=14)

### UX Designs

[![](./qr-send-receive-money-2.png)https://www.figma.com/file/L3WF3edAZXHf5EsOjojXXj/QR-Scan-%26-Pay?node-id=59%3A23212](https://www.figma.com/file/L3WF3edAZXHf5EsOjojXXj/QR-Scan-%26-Pay?node-id=59%3A23212) - Connect your Figma account

|     |     |     |     |     |
| --- | --- | --- | --- | --- |
| Scope<br>----- | MVP - Send Money<br>---------------- | MVP - Receive Money<br>------------------- | MMP - Send Money<br>---------------- | MMP - Receive Money<br>------------------- |
| Entry point | Move Money hub | Move Money hub | Pre-login screen | Pre-login screen |
| QR code scanning | Camera | -   | Choose from gallery | -   |
| My personal QR | -   | * Static QR<br>    <br>* Share QR | -   | * Dynamic QR<br>    <br>* Update/Refresh QR |
| Transfer amount | User input | -   | Pre-filled amount from QR | User input (receiver) |
| Account Selection | Full list | Default account | Filtered list | * Full list<br>    <br>* Filtered list |
| Description | * Optional<br>    <br>* Mandatory<br>    <br>* Character length<br>    <br>* Special characters | -   |     | -   |
| Review | Display transfer details | -   |     | -   |
| Success screen | * Timestamp<br>    <br>* Reference number<br>    <br>* Share successful transfer | -   |     | -   |
| Failure screen | * Display error codes<br>    <br>* Display error messages | -   | Error code configuration | -   |
| Generic features | * No internet connection<br>    <br>* Loading error<br>    <br>* FE validations for individual component<br>    <br>    * Adding of components<br>        <br>        * Initiate screen<br>            <br>        * Review screen<br>            <br>* Order of the components<br>    <br>* Edit components<br>    <br>    * Group<br>        <br>* API validations<br>    <br>    * By screen<br>        <br>    * Individual components |     | * Navigation at the end of the journey<br>    <br>* Adding of components<br>    <br>    * Initiate screen<br>        <br>    * Review screen<br>        <br>* Order of the components<br>    <br>    * Edit<br>        <br>    * Group<br>        <br>* API validations<br>    <br>    * By screen<br>        <br>    * Individual components |     |