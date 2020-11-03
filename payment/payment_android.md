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
let cttConfig = CTTPaymentConfig(partnerCode: partnerCode)
try PaymentGateway.addConfig(cttConfig, forMethod: .qr)
```

Finally, add payment methods to the `gateway`

```swift
PaymentGateway.addPaymentMethods([.qr, .spos])
```

# Create transaction

```swift
let request = CTTTransactionRequest(orderId: orderId,
                                    orderCode: orderCode,
                                    amount: amount, 
                                    expireTime: expireTime)                                     
```

```swift
try paymentGateway.pay(method: .qr, request: request, completion: { result in
    switch result {
    case .success(let transaction):
        // Do something
    case .failure(let error):
        // Handle error
    }
})  
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