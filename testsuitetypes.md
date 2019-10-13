# Symbiote Test Suite Definitions

## Topic

A `Topic` is just an alias for a String, whatever that is for your platform. When serialized, it will be
UTF-8 encoded.

```haskell
data Topic = Topic String
```

### Generation

Generating Unicode strings can be tricky business. It is common for operating systems to treat surrogate
characters as special, and should therefore be avoided. See [this Wikipedia article on UTF-16](https://en.wikipedia.org/wiki/UTF-16#U+D800_to_U+DFFF) (which is what JavaScript uses under-the-hood) for more details.

```haskell
generate :: Gen Topic
generate = do
  characters <- arrayOf (arbitraryChar `suchThat` isNotSurrogate)
  return (Topic (arrayToString characters))
```

### JSON

`Topic` values are encoded as a plain JSON string.

```haskell
encodeJson :: Topic -> Json
encodeJson (Topic t) = stringAsJson t
```

### Binary

This protocol is required to support JavaScript, which does not support 64-bit integers natively. Therefore,
the encoding mechanism used here will do a standard UTF-8 encoding with a 32-bit size index of the bytestring.

```haskell
encodeBinary :: Topic -> ByteString
encodeBinary (Topic t) =
  (intAsByteString (length t :: Int32))
    ++ (stringAsByteString t)
```

## Size

JavaScript does not support 64-bit integers, so it is our lowest-common-denominator for this test suite,
and we should use 32-bit integers whenever we need an `Int`.

```haskell
data Size = Size Int32
```

### Generation

```haskell
generate :: Gen Size
generate = do
  size <- between 0 (2^31 - 1)
  return (Size size)
```

### JSON

JavaScript can handle 32-bit integers, so we can directly use our `Size` in a JSON document.

```haskell
encodeJson :: Size -> Json
encodeJson (Size s) = intAsJson s
```

### Binary

```haskell
encodeBinary :: Size -> ByteString
encodeBinary (Size s) = intAsByteString s
```

## AvailableTopics

In a platform's implementation, an `AvailableTopics` is just a mapping from `Topic`s to `Size`s -
this could be a HashMap, a B-tree map, or a JSON object.

```haskell
data AvailableTopics = AvailableTopics (Map Topic Size)
```

### Generation

```haskell
generate :: Gen AvailableTopics
generate = do
  pairs <- arrayOf (pairOf arbitraryTopic arbitrarySize)
  return (AvailableTopics (arrayToMap pairs))
```

### JSON

This data type is the same as a JSON object of integers, so we'll use that for its JSON encoding.

```haskell
encodeJson :: AvailableTopics -> Json
encodeJson (AvailableTopics ts) = mapAsJsonObject ts
```

### Binary

A `Topic` tells us how many bytes it uses in its first 32-bit value, while `Size` is always 4 bytes
because its a 32-bit integer. Therefore, a pair of a `Topic` and `Size` can be stored right next
to each other, and the only thing we have have to worry about is storing _how many_ pairs there are.

```haskell
encodeBinary :: AvailableTopics -> ByteString
encodeBinary (AvailableTopics ts) =
  (intAsByteString (length ts :: Int32))
    ++ (arrayAsByteString (map pairToByteString (mapToArray ts)))
  where
    pairToByteString :: (Topic, Size) -> ByteString
    pairToByteString (t,s) = (encodeBinary t) ++ (encodeBinary s)
```
