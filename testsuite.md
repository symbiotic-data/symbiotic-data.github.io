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
