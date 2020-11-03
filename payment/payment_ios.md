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

```swift
let cttMethod = CTTMethod(config: .init(), methodCode: CTTMethod.cttCode)
let atmMethod = ATMMethod(config: .init(), methodCode: ATMMethod.atmCode)
Minerva.shared.setPaymentMethods(methods: [cttMethod, atmMethod])
```

Finally, add payment methods to the `gateway`

```swift
PaymentGateway.addPaymentMethods([.qr, .spos])
```

# Create transaction

```swift
let request = CTTTransactionRequest(orderId: orderId,
                                    orderCode: orderCode,
                                    amount: amount)                                     
```

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
observer.observe(transactionCode: transactionCode) { result in
    switch result {
    case .success:
        // Success, order is paid
    case .failure(let error):
        // Failed
    }
}
```