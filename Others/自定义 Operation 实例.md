
# AsyncOperation

```Objective-C
@class AsyncOperation;
typedef void (^AsyncBlock)(AsyncOperation * asyncOperation);

@interface AsyncOperation : NSOperation

+ (instancetype)blockOperationWithBlock:(AsyncBlock)block;
- (instancetype)initWithBlock:(AsyncBlock)block;

@end
```

```Objective-C
@interface AsyncOperation ()

@property (assign, nonatomic, getter = isExecuting) BOOL executing;
@property (assign, nonatomic, getter = isFinished) BOOL finished;
@property (nonatomic, copy, nonnull) AsyncBlock executionBlock;

@end

@implementation AsyncOperation

@synthesize executing = _executing;
@synthesize finished = _finished;

- (void)start {
    if (self.isCancelled) {
        return;
    }
    
    [self willChangeValueForKey:@"isExecuting"];
    self.executing = YES;
    [self didChangeValueForKey:@"isExecuting"];
    
    if (self.executionBlock) {
        self.executionBlock(self);
        return;
    }
    
    [self done];
}

- (void)cancel {
    [super cancel];
    [self done];
}

- (void)done {
    [self willChangeValueForKey:@"isExecuting"];
    [self willChangeValueForKey:@"isFinished"];
    self.executing = NO;
    self.finished = YES;
    [self didChangeValueForKey:@"isExecuting"];
    [self didChangeValueForKey:@"isFinished"];
}

@end
```

# AsyncOperationQueue
```Objective-C
@interface AsyncOperationQueue : NSOperationQueue

@property (nonatomic, strong) void(^_Nullable operationsFinishedBlock)(void);

@end
```

```Objective-C
NSString* kOperationCount = @"operationCount";
static NSString *kQueueOperationsChanged = @"kQueueOperationsChanged";

@implementation AsyncOperationQueue

- (instancetype)init {
    self = [super init];
    if (self) {
        [self installObserver];
    }
    return self;
}

- (void)dealloc {
    [self uninstallObserver];
}

- (void)installObserver {
    [self addObserver:self forKeyPath:kOperationCount options:NSKeyValueObservingOptionNew|NSKeyValueObservingOptionOld context:NULL];
}

- (void)uninstallObserver {
    [self removeObserver:self forKeyPath:kOperationCount];
}

- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context {
    if ([keyPath isEqualToString:kOperationCount]) {
        if (self.operationCount == 0) {
            if (self.operationsFinishedBlock) {
                self.operationsFinishedBlock();
            }
        }
    }
    else {
        [super observeValueForKeyPath:keyPath ofObject:object change:change context:context];
    }
}

@end
```
