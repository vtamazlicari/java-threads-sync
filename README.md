# thread-java

A small Java example of thread coordination using `wait()` and `notifyAll()`.

## What it does

Three threads share one `NotifyAllExample` object, which acts as both the lock and the holder of a `status` field (starting at `1`). Each thread waits on that object until `status` has the value it needs, prints its number, sets `status` to `3`, and calls `notifyAll()` to wake the others.

| Thread    | Waits until  | Prints | Then sets |
|-----------|--------------|--------|-----------|
| `Thread3` | `status == 1` | `3`    | `status = 3` |
| `Thread1` | `status == 3` | `1`    | `status = 3` |
| `Thread2` | `status == 3` | `2`    | `status = 3` |

`Thread3` always prints first, because it is the only thread that can run while `status` is `1`. After that, `Thread1` and `Thread2` are both free to run, so their order depends on which one the scheduler hands the lock to first:

```
3 1 2
```

or

```
3 2 1
```

The point of the example is the standard pattern for waiting on a condition:

- call `wait()`/`notifyAll()` only while holding the lock (`synchronized`)
- check the condition in a `while` loop, not an `if`, to handle spurious wakeups
- use `notifyAll()` so every waiting thread re-checks its condition

## Project layout

```
src/thread/
  NotifyAllExample.java   main class, holds the shared status and starts the threads
  Thread1.java
  Thread2.java
  Thread3.java
```

It is an Eclipse project (`.project`, `.classpath`), set to Java 1.8. There are no external dependencies.

## Running

In Eclipse, import the folder as an existing project and run `NotifyAllExample`.

From the command line:

```sh
javac -d out src/thread/*.java
java -cp out thread.NotifyAllExample
```
