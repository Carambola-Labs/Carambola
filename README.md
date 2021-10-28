# Substrate Carambola Node


## Build & Run

To build the chain, execute the following commands from the project root:

```
$ cargo build --release
```

To execute the chain, run:

```
$ ./target/debug/carambola-node --dev
```

The node also supports to use manual seal (to produce block manually through RPC).  
This is also used by the ts-tests:

```
$ ./target/debug/carambola-node --dev --manual-seal
```

```


