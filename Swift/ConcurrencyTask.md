# What’s the difference between a task and a detached task?

一句话总结，`Task{}`会按顺序`同步`调用，而`Task.detached{}`会异步调用，一般只有在尝试了常规任务和异步任务(async let)仍然解决不了某些问题时才用到。

https://www.hackingwithswift.com/quick-start/concurrency/whats-the-difference-between-a-task-and-a-detached-task
