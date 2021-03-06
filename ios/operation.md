# `NSOperation` aka `Operation`

* from [nshipster](http://nshipster.com/nsoperation/)
* apple [docs](https://developer.apple.com/documentation/foundation/operation)
* [readme](https://www.raywenderlich.com/190008/operation-and-operationqueue-tutorial-in-swift)

* [readme](https://agostini.tech/2017/07/30/understanding-operation-and-operationqueue-in-swift/)
* [stack overflow readme](https://stackoverflow.com/questions/43561169/trying-to-understand-asynchronous-operation-subclass)
* [readme](https://medium.com/flawless-app-stories/parallel-programming-with-swift-operations-54cbefaf3cb0)

`NSOperation` represents a single unit of work. It’s an abstract class that offers
a useful, thread-safe structure for modeling state, priority, dependencies, and
management.

For situations where it doesn’t make sense to build out a custom `NSOperation`
subclass, Foundation provides the concrete implementations `NSBlockOperation` and
`NSInvocationOperation`.

Examples of tasks that lend themselves well to `NSOperation` include network
requests, image resizing, text processing, or any other repeatable, structured,
long-running task that produces associated state or data.

But simply wrapping computation into an object doesn’t do much without a little
oversight. That’s where `NSOperationQueue` comes in.


* like a dispatch group
* state machine
* can monitor with kvo
* can declare dispatch queues as the operation queues underlying queue
* dont need to start an operation queue
* when you add an operation, itll start as soon as its ready
* `waitUntilAllOperationsAreFinished`
* `maxConcurrentOperationCount`
* `serialOperationQueue`

## State machine
* `isCancelled`
* `isConcurrent`
* `isAsynchronous`


## methods

### `start()`
* change state to `executing` or `finisehd` (if `cancelled`)
* call `main`
* trigger KVO for state change

### `main()`

### `cancel()`

### `waitUntilFinished()`

### initially
```swift
isReady = false
isExecuting = false
isFinished = false
```

### ready
```swift
isReady = true  // understands dependency
isExecuting = false
isFinished = false
```

### executing
```swift
isReady = true  
isExecuting = true // do something to make it true, kick off async task
isFinished = false
```

### finished
```swift
isReady = true
isExecuting = false
isFinished = true // happens in the completion handler
```

* relies on KVO so others know state changed
* chain async calls in an operation queue
* avoid pyramid of doom
* more complex dependencies

* to do dependencies, the queue must know when it's finished
* the state must manage its state changes


If you do not want to use an operation queue, you can execute an operation yourself by calling its start() method directly from your code.

### starting

If you do not want to use an operation queue, you can execute an operation
yourself by calling its `start()` method directly from your code. Executing
operations manually does put more of a burden on your code, because starting an
operation that is not in the ready state triggers an exception. The `isReady`
property reports on the operation’s readiness.


## dependencies
By default, an operation object that has dependencies is not considered ready
until all of its dependent operation objects have finished executing. Once the
last dependent operation finishes, however, the operation object becomes ready
and able to execute.

The dependencies supported by `NSOperation` make no distinction about whether a
dependent operation finished successfully or unsuccessfully. (In other words,
canceling an operation similarly marks it as finished.) It is up to you to
determine whether an operation with dependencies should proceed in cases where
its dependent operations were cancelled or did not complete their task
successfully. This may require you to incorporate some additional error tracking
capabilities into your operation objects.


## `isAsynchronous`
When you add an operation to an operation queue, the queue ignores the value of
the `isAsynchronous` property and always calls the `start()` method from a separate
thread. Therefore, if you always run operations by adding them to an operation
queue, there is no reason to make them asynchronous.


## Methods to Override

For non-concurrent operations, you typically override only one method:

```swift
# if a sync method

# isExecuting = true
# essentially it calls you the start method for you when in sync mode
`main()`
# isExecuting = false
```

if it's synchronous, then once you leave the main method you're considered to be done.

Into this method, you place the code needed to perform the given task. Of
course, you should also define a custom initialization method to make it easier
to create instances of your custom class. You might also want to define getter
and setter methods to access the data from the operation. However, if you do
define custom getter and setter methods, you must make sure those methods can be
called safely from multiple threads.

If you are creating a concurrent operation, you need to override the following
methods and properties at a minimum:

## `start()`

The default implementation of this method updates the execution state of the
operation and calls the receiver’s `main()` method.


`isAsynchronous`

`isExecuting`

`isFinished`


## `cancel()`
* just a bool flipping
* observe it changing, and react accordingly
* susceptible to race conditions
* cannot go from `finished` to `cancel`


## `main()`

The default implementation of this method does nothing. You should override this
method to perform the desired task. In your implementation, do not invoke `super`.
This method will automatically execute within an autorelease pool provided by
`NSOperation`, so you do not need to create your own autorelease pool block in
your implementation.

If you are implementing a concurrent operation, you are not required to override
this method but may do so if you plan to call it from your custom `start()`
method.


* as soon as you add the operation to the queue it is going to start

### Operation Queue

* high-level `dispatch_queue_t`
* makes it easy to things that arent yet executing

```swift
// send cancel message to all operations
operationQueue.cancel()
```


### Operation
* high-level `dispatch_block_t`

* pending
* ready
* executing
* finished

![img](../images/operation-state-machine.png)


### Ready
* a simple bool
* means operation ready to execute
