---
layout: post
title: "Rewind view controller states (observables)"
date: 2016-07-19 23:54:09 +0800
categories: swift, ios, rxswift, rxcocoa
keywords: "swift, ios, rxswift, rxcocoa"
---
A pattern that was not immediately obvious when you began to use an [Rx](https://github.com/ReactiveX/RxSwift)-based view controller setup is to undo/reset all of the observables that have been subscribed to after it re-appears.

This is a common behaviour in a navigation controller stack of view controllers. We might have various controls in a view controller, it could be a text field, text view, or anything that you might want to return to from the next controller in a navigation stack.

```swift
import UIKit
import RxSwift
import RxCocoa

final class FormViewController: UIViewController {
    var disposeBag: DisposeBag = DisposeBag()

    var completed: PublishSubject<Void>()

    lazy var textField: UITextField = {
        let textField = UITextField()
        textField.autocorrectionType = .No
        textField.autocapitalizationType = .None
        textField.returnKeyType = .Done
        return textField
    }()

    func viewDidLoad() {
        super.viewDidLoad()

        // We indicate that a certain action should be completed,
        // once the text field's text reaches a certain length
        textField.rx_text
            .asObservable()
            .subscribeNext { text in
                if text.length > 6 { 
                    completed.onCompleted()
                }
            }
            .addDisposableTo(disposeBag)
    }

    func viewWillAppear(animated: Bool) { 
        super.viewWillAppear(animated)

        // Observe when returning
        completed = PublishSubject<Void>()
        completed
            .subscribeCompleted { 
            // A subscribed action is completed
        }
        .addDisposableTo(disposeBag)
    }

    func viewWillDisappear(animated: Bool) { 
        super.viewWillDisappear(animated)

        // Clean up all observables
        disposeBag = DisposeBag()
    }
}
```

Typically, we would want to do a certain action in a view controller, that could be observed, and it should be known when does it complete. One example is network request, but the above example is simplified to illustrate the idea of running an action that is triggered by an observed event (text field event). [PublishSubject](http://reactivex.io/documentation/subject.html) is a common example to represent an action subject that can be subscribed to and could subscribe to an observable.

In the snippet above, on view appearance, the action is being reset, and re-subscribed, while on view disappearance, the disposeBag is being re-created. Once it returns to this view controller from the navigation stack it will have the completed subject re-created and re-subscribed.

Without the above setup, a completed action remains completed and it will not emit anymore items that is being observed.

To think about it a little bit further, before Rx I often do not have an idea on how to “replay” a certain action within scenes of view controllers. Traditionally this can be done by re-creating whatever it’s an object responsible for such action (e.g. NSURLRequest).

It can always be done traditionally without Rx, but in that way there doesn’t seem to be a generalisation of idea in how to represent an action that can be anything.
