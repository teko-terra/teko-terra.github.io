# Installation

Using **[CocoaPods](https://cocoapods.org/)**

Add `Minerva` pod to your `Podfile`

```ruby
pod 'Minerva', :source => 'https://github.com/teko-vn/Specs-ios'
```

Don't forget to execute command `pod install`

# Configuration

Before using Minerva, we have to set up a `MinervaConfig`

```swift
let config = MinervaConfig(
    clientCode,     // payment client code, provided by Payment service
    terminalCode,   // payment terminal code, provided by Payment service
    serviceCode,    // payment service code, provided by Payment service
    secretKey,      // payment secret key, provided by Payment service
    baseUrl         // payment service base url
)
```

# Add payment methods

`Minerva` currently supports following payment methods
- QR code via CTT
- ATM via VNPay gateway
- SPOS

Before create a transaction with one of these methods, you need to add it by `setPaymentMethods`

```swift
let cttMethod = CTTMethod(config: .init(), methodCode: CTTMethod.cttCode)
let atmMethod = ATMMethod(config: .init(), methodCode: ATMMethod.atmCode)
Minerva.shared.setPaymentMethods(methods: [cttMethod, atmMethod])
```

Finally, add payment methods to the `Minerva`

```swift
Minerva.shared.addPaymentMethods([cttMethod, atmMethod])
```

# Create transaction

First we need to create a transaction request.

```swift
let request = CTTTransactionRequest(orderId: orderId,
                                    orderCode: orderCode,
                                    amount: amount)                                     
```

And then call `pay` function with a respective method along with this request.

```swift
do {
    try Minerva.shared.pay(method: CTTMethod.cttCode, request: request, completion: { result in
        switch result {
        case .success(let transaction):
            onSuccess(QRCodeData(transactionCode: transaction.code, qrContent: transaction.qrContent))
        case .failure(let error):
            onError(error.localizedDescription)
        }
    })
} catch {
    onError(error.localizedDescription)
} 
```

# Observe transaction result

In the screen where you want to observe the transaction result, you need to create a `PaymentObserver`

```swift
let observe = PaymentObserver()
observer.observe(transactionCode: transactionCode) { result in
    switch result {
    case .success:
        // Success, order is paid
    case .failure(let error):
        // Failed
    }
}
```