# [iOS] How to: Migrate an existing Widget into a Journey
*   1 [Introduction](#Introduction)
*   2 [Pre-requisites](#Pre-requisites)
*   3 [Migration](#Migration)
*   4 [Using the Mobile Design System](#Using-the-Mobile-Design-System)
*   5 [How to present the Widget](#How-to-present-the-Widget)
*   6 [Navigating inside the Widget](#Navigating-inside-the-Widget)

# Introduction

In the world of Widget Collection, the Mobile SDK (MSDK) is responsible for rendering the Widget. The consequence is that the UI layer (`NativeView`) is tightly coupled with the business logic layer (`Widget`): these two elements have a reference to each other. This can become problematic if we are using a different navigation mechanism.

Because the Journey architecture no longer uses the pub/sub navigation system from the Widget architecture, we will instead use the navigation controller from UIKit to navigate between screens and decouple our Widget view from the business logic layer.

This guide will demonstrate how to migrate an existing Widget into an app using the Journey architecture.

# Pre-requisites

1.  Resolver is used for dependency injection. If you’re using already an out-of-the-box Journey or a productized app, Resolver is already included. Otherwise, import it in your Podfile using the following line:
    
    `pod 'Resolver'`
    

# Migration

As an example, we will work with a Widget named `CryptoWidget`.

1.  First, ensure your Widget contract protocol extends `NativeRenderer`. `NativeRenderer` is an implementation of both `Renderer` and `NativeContract`, which will allow you to use it to initialize your Widget views.
    
    `protocol CryptoWidgetContract: NativeRenderer { var btcPrice: BehaviorSubject<String> { get } func getBtcPrice() func getBtcTradingExchanges() -> [String] }`
    
2.  We can create an implementation of this protocol using the following sample Widget code (Note that this implementation uses RxSwift and RxCocoa such that `btcPrice` observes any changes to its string value:
    
    `import Foundation import Backbase import RxSwift import RxCocoa class CryptoWidget: NativeRenderer, CryptoWidgetContract { var btcPrice: BehaviorSubject<String> = .init(value: "") func getBtcPrice() { guard let url = URL(string: "https://api.coincap.io/v2/assets/bitcoin") else { return } let dataTask: URLSessionDataTask = URLSession.shared.dataTask(with: url, completionHandler: { [weak self] data, response, error in guard error == nil, let data = data else { return } if let json = try? JSONSerialization.jsonObject(with: data, options: []) as? [String : Any], let jsonData = json["data"] as? [String : Any], let price = jsonData["priceUsd"] as? String { self?.btcPrice.onNext(price) } }) dataTask.resume() } func getBtcTradingExchanges() -> [String] { return ["Coinbase", "Kraken", "Binance"] } }`
    
3.  Using Resolver, register this Widget contract inside the `AppDelegate`. This will allow you to inject this instance from inside the Widget view.
    
    `override init() { super.init { sdk, design in return { appConfig in ... } } Resolver.register { CryptoWidget() as CryptoWidgetContract } }`
    
4.  Your Widget view should extend `UIView` and implement `NativeView`. The `NativeView` protocol, by default, provides an `initialize` method, which is where any setup code should belong.
    
    `class CryptoPriceWidgetView: UIView, NativeView { public static func initialize(with contractImpl: NativeContract, container: UIView) -> (UIView & NativeView)? { guard let view: CryptoPriceWidgetView = CryptoPriceWidgetView.loadFromNib() else { return nil } container.addSubview(view) ... return view } }`
    
5.  Because the Journeys architecture uses Resolver to inject dependencies, you can create a member property inside your Widget view that conforms to `CryptoWidgetContract`. This property should be injected from the registered instance using Resolver instead of being retrieved from the `initialize` method. In doing so, you are tying the contract to instance of the Widget view rather than assigning it through the static `initialize` method.
    
    `private let contract: CryptoWidgetContract? = Resolver.optional()`
    
6.  For the purposes of this Widget, we can observe any changes in the contract by binding a label to the observable string value from `CryptoWidgetContract`. We can also use a button that calls the contract’s `getBtcPrice()` method to trigger this change.
    
    `class CryptoPriceWidgetView: UIView, NativeView { ... let disposeBag = DisposeBag() @IBOutlet weak var stackView: UIStackView? @IBOutlet weak var priceLabel: UILabel? @IBOutlet weak var priceButton: Button? @IBOutlet weak var exchangeButton: Button? @IBAction func didTapPriceButton() { contract?.getBtcPrice() } internal func setup() { ... contract?.btcPrice.bind(onNext: { priceString in DispatchQueue.main.async { [weak self] in self?.priceLabel?.text = priceString } }).disposed(by: disposeBag) } ... }`
    

# Using the Mobile Design System

It is best practice to utilize the features of the Backbase Mobile Design System when building a Widget view. In the snippet below, note that we are applying the `DesignSystem.shared` styles to our buttons, which conform to type `Button` instead of `UIButton`. These features can be found in the `BackbaseDesignSystem` and `RetailJourneyCommon` modules.

Note also that these buttons use instances of `LocalizedString` to assign text values so that these strings can adapt to the user’s locale.

`import BackbaseDesignSystem import RetailJourneyCommon class CryptoPriceWidgetView: UIView, NativeView { ... @IBOutlet weak var priceButton: Button? @IBOutlet weak var exchangeButton: Button? internal func setup() { priceButton?.setTitle(LocalizedString(deferredValue: "Retrieve").value, for: .normal) exchangeButton?.setTitle(LocalizedString(deferredValue: "See Exchanges").value, for: .normal) if let priceButton = priceButton { DesignSystem.shared.retailStyles.button(.primary)(priceButton) } if let exchangeButton = exchangeButton { DesignSystem.shared.retailStyles.button(.primary)(exchangeButton) } ... } }`

# How to present the Widget

In the Widget architecture, you would use `Backbase.publish(event:payload:)` to notify of any navigation changes. However, for the Journey architecture, we can instead use the UIKit navigation controller to push new view controllers to the stack.

Since each Widget view is an implementation of `NativeView`, you can call the `initialize(contractImpl:container:)` method by passing in the registered instance of your Widget contract and the view of a fresh view controller you have just instantiated.

`private static func cryptoMenuItemAction(navigationController: UINavigationController) { guard let cryptoWidget: CryptoWidgetContract = Resolver.optional() else { return } let viewController = UIViewController() _ = CryptoPriceWidgetView.initialize(with: cryptoWidget, container: viewController.view) navigationController.pushViewController(viewController, animated: true) }`

To trigger this method, we can attach it to the existing configuration of the More Menu.

`extension More { static func configure(_ configuration: inout More.Configuration) { let cryptoMenuItem = More.MenuItem(title: .init(value: "Crypto"), icon: nil, action: cryptoMenuItemAction(navigationController:)) let cryptoSection = More.MenuSection(title: .init(value: "Crypto"), items: [cryptoMenuItem]) let deferredSections = configuration.menu.deferredSections configuration.menu.deferredSections = { guard let deferredSections = deferredSections else { return [] } var sections = deferredSections() sections.append(cryptoSection) return sections } } }`

# Navigating inside the Widget

Let’s say you wish to navigate to another view from your current Widget view. Normally, this would be done using the same pub/sub mechanism from the Widget architecture, which we have just replaced using a `UINavigationController`. The same approach applies if you are trying to navigate to a new Widget view.

Say you have a button in your Widget view that is supposed to lead to a new screen. You can attach a selector that contains the following code.

`@IBAction func didTapExchangeButton() { guard let contract = contract else { return } let viewController = UIViewController() _ = CryptoExchangesWidgetView.initialize(with: contract, container: viewController.view) if let tabBarController = UIApplication.shared.keyWindow?.rootViewController as? UITabBarController, let navigationController = tabBarController.selectedViewController as? UINavigationController { navigationController.pushViewController(viewController, animated: true) } }`

First, create a fresh view controller. Then initialize your new Widget by calling its static `initialize` method and passing in the contract already attached to your current Widget view. Your view controller should now be referencing the Widget view that you wish to display.

Note here that we are first finding a `UITabBarController` as the root view controller since the Journey architecture uses a tab bar by default. This tab bar controller contains a nested `UINavigationController`, which is what we will use to push a newly instantiated view controller.