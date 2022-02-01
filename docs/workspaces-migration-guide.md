# Workspaces Migration Guide
TODO: write about context, why simtests was not suitable as a testing framework and how workspaces is different

## Transitioning existing near-sdk-sim powered tests to workspaces
As an example, let's take a look at transitioning from near-sdk-sim `4.0.0-pre.6` to workspaces `0.1.1`. Given that near-sdk-sim is going to be deprecated, it is very unlikely that its API is going to ever change, but future releases of workspaces might. Hopefully this guide is going to be helpful even if you are migrating your project to a more recent version, but, if so, keep in mind that the provided snippets might not work as is, in which case refer to workspaces release notes to see how public API has changed since `0.1.1`.

### Async runtime and error handling
Chances are your tests look something like this:
```rust
#[test]
fn simulate_transfer() {
    ...
}
```

First big change is that workspaces API is asynchronous, meaning that contract function calls return values that implement `Future` trait. This means that you will not be able to operate on the call results in a synchronous environment, thus you will have to add an async runtime (if you do not already have one). In this guide we are going to be using `tokio`. Rewrite the test above like this:

```rust
#[tokio::test]
async fn simulate_transfer() {
    ...
}
```

The second change is that workspaces makes an extensive use of [`anyhow::Result`](https://docs.rs/anyhow/latest/anyhow/type.Result.html). Although you can work with `Result` directly, our recommendation is to make your tests return `anyhow::Result<()>` like this:

```rust
#[tokio::test]
async fn simulate_transfer() -> anyhow::Result<()> {
    ...
}
```

This way you can use `?` anywhere inside the test to safely unpack any `anyhow::Result<R>` type to `R` (will be very useful further down the guide). Note that the test will fail if `anyhow::Result<R>` cannot be unpacked.

### Initialization
The usual way to initialize simulator looks like this:

```rust
let root = near_sdk_sim::init_simulator(...);
```

For workspaces, we use an actual NEAR node

### Making transactions

### Making view calls

## Caveats
### Batched transactions
### Custom genesis
### Inspecting logs
