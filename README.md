# tsx_test

These benchmarks intended to provide some  data about what to expect from [RTM](https://en.wikipedia.org/wiki/Transactional_Synchronization_Extensions) in java under contention and without it in comaprison to normal execution with RTM disabled in simplistic case.

## Environment:
### HW
Intel i5 6600k  
DDR4 2400MHz

### SW
Ubuntu 17.10  
`java -version`:

```
java version "9.0.1"  
Java(TM) SE Runtime Environment (build 9.0.1+11)  
Java HotSpot(TM) 64-Bit Server VM (build 9.0.1+11, mixed mode)
```

## Results

With TSX:

`java -jar target/benchmarks.jar -t 4 -jvmArgs -XX:+UseRTMLocking`
```
Benchmark                  Mode  Cnt          Score          Error  Units
MyBenchmark.globalTest    thrpt  200   16126725.568 ±   144057.622  ops/s
MyBenchmark.threadedTest  thrpt  200  121607310.904 ± 13629294.061  ops/s
```
Without TSX:


`java -jar target/benchmarks.jar -t 4`
```
Benchmark                  Mode  Cnt         Score        Error  Units
MyBenchmark.globalTest    thrpt  200  24939497.871 ± 419531.246  ops/s
MyBenchmark.threadedTest  thrpt  200  25886964.240 ± 359428.991  ops/s
```

In the `globalTest` benchmark 4 threads concurrently changes a value of a volatile boolean field in the shared state, while synchronizing on a single monitor.
 
In the `threadedTest` benchmark 4 threads change the value of volatile boolean fields in thread-scoped states while synchronizing on a single monitor.
 
So it seems that in my case benchmark code with RTM enabled operates 1.5 worse than in the case when RTM is disabled when contention is present.  
On the other hand, same code operates 4.7 times more performant with RTM in case there's no contention. 
