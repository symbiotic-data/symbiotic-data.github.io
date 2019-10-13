# Symbiote Test Suite

The test suite is a simple idea - there are two peers `A` and `B` (each on potentially different platforms),
with some peer-to-peer communications channel between them `Socket`
(for instance, [ZeroMQ](https://zeromq.org) or a WebSocket). `Socket` only understands some target data type,
we'll call `Target` (for instance, `ByteString` or `ArrayBuffer` for ZeroMQ, and `Json` for WebSockets).

```
 ________                   ________
| Peer A |      Socket     | Peer B |
|        |  -------------- |        |
|________|     (Target)    |________|
```

Now, both `Peer A` and `Peer B` understand some "idea" of a data type, we'll call `Type T`. Now, the
whole purpose of this test suite is to verify that both peers have _identical_ understandings of `Type T`,
with respect to how `T` _operates_, __and__ how it _translates_ to-and-from `Target`.

So, each peer has an idea of what `T` is, and some static set of operations that can be performed on `T`.
The "operations" on `T` need to be extrapolated into a data type as well, such that every return type
when performed on a `T` is homogeneous. For instance, if I have functions `f :: T -> T` and `g :: T -> Bool`,
then I need to come up with a data type that looks like this:

```haskell
data OperationsOnT
  = F
  | G

perform :: OperationsOnT -> T -> Either T Bool
perform op x = case op of
  F -> Left (f x)
  G -> Right (g x)
```

Where `Either T Bool` is the "homogeneous return type" of _all_ the supported operations on my type `T`.

Now, our idea of the network might look like this:

```
 ________                   ________
| Peer A |      Socket     | Peer B |
|        |  -------------- |        |
|________|     (Target)    |________|
|    T   |                 |   T    |
 \______/                   \______/ 
```

## First and Second

Now comes the actual meat of the protocol.

The protocol assumes its being run on a 1-to-1 communication mechanism - there's no testing for broadcasting
and subscribed messages; only one other peer will be tested at the same time. This implies a constraint on
how our protocol will operate - one peer needs to be the "first" to operate, and the other needs to be the
"second". It's not rocket science - if we're generating data and expecting the other party to operate on that
data, then someone has to go first while the other listens for it. If they both went at the same time, there
would be a race condition. If they both were expecting data, then there'd be a deadlock. Therefore, statically,
there should be some decision made by the programmer (you) on which peer will be the "first" and which will
be "second". It doesn't make a difference in the semantic integrity of the test suite - they both will
generate and consume data.

For simplicity's sake, let's make `Peer A` the first, and `Peer B` the second.


```
 ________                   ________
|  First |                 | Second |
|________|                 |________|
| Peer A |      Socket     | Peer B |
|        |  -------------- |        |
|________|     (Target)    |________|
|    T   |                 |   T    |
 \______/                   \______/ 
```


## Generation

Now, both peers will take turns in generating, consuming, and exchanging data. `First` will be, you guessed it,
the first one to take a step in that dance. Before they start, though, let's talk about data generation for
a quick second.

If you've ever heard of [QuickCheck](https://hackage.haskell.org/package/QuickCheck) or property-based testing,
this should come to no surprise for you. For everyone else, though, it might seem a bit off-color. So let me
just say it now - generating random data implies that there's some "size" associated with how large that data
should be. Think about it; if we just didn't care about the general size of that random data, and we were
generating an array, the computer could just generate an infinite set of data willy-nilly. What's going to
tell it to stop? A `size` parameter, that's what.

So, in our test suite, every data type we'll be verifying (through generation) __must__ have some maximum
size (as an integer), which we'll approach starting from `0`.
