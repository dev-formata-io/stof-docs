---
description: Asynchronous Stof.
---

# Async

There is no separation between asynchronous and synchronous Stof. The default Stof runtime is async by default, and it is up to the libraries you have loaded into your document to manage multi-threaded interactions with the runtime.

{% hint style="info" %}
An example of a multi-threaded async process is the `Http.fetch(..)` function in the [http-network-library-http.md](../libraries/http-network-library-http.md "mention"), which creates a thread pool for parallel HTTP requests in the background.
{% endhint %}

{% hint style="info" %}
Take a look at [functions.md](functions.md "mention") for more information on async functions & expressions.
{% endhint %}

To separate terminology from Rust a bit, we use the name "process" in Stof to denote an asynchronous task.

Each process can be running, waiting, sleeping, done, or errored, with the default runtime making progress on all processes at once.

For example, this main function is a process that will create two additional processes. These processes will all make progress at the same time on a single thread as the runtime will switch between them periodically.

{% hint style="info" %}
For `stof run` & `stof test`, each #\[main] or #\[test] function always creates a new runtime process (async).
{% endhint %}

```rust
async fn counting_up(now: ms, n: int) -> ms {
    for (let i = 0; i < n; i += 1) {}
    Time.diff(now)
}

#[main]
fn main() {
    const now = Time.now();
    const time_to_count = self.counting_up(now, 10_000);
    const loop_time = async {
        let count = 0;
        loop {
            count += 1;
            if (count >= 10_000) break;
        }
        Time.diff(now)
    };

    // can await each promise or a list
    pln(await [time_to_count, loop_time]);
}
```

```bash
> stof run
[27ms, 28ms]
```

These two times being roughly equivalent means that these loops are executing at the same time, sharing a single thread to make progress (since they both use the same starting timestamp).
