# Symbiotic Data

## Boolean

```haskell
data Boolean = True | False
```

### JSON

Uses standard JSON Boolean

```haskell
encodeJson :: Boolean -> Json
encodeJson x = case x of
  True -> true
  False -> false
```

### Binary

Uses standard bytes `0` as false, `1` as true

```haskell
encodeBinary :: Boolean -> ByteString
encodeBinary x = case x of
  True -> byteAsByteString 1
  False -> byteAsByteString 0
```

## Integral

### Signed

#### Int8

Range varies from `-2^7` to `2^7-1`

```haskell
data Int8 = -2^7 | -2^7+1 | ... | 2^7-2 | 2^7-1
```

##### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Int8 -> Json
encodeJson x = intAsJson x
```

##### Binary

`Int8`s are converted to `Uint8`s before storing as a byte - where the negative range is stored as
the upper values in the `Uint8`.

```haskell
encodeBinary :: Int8 -> ByteString
encodeBinary x =
  if x >= 0
  then byteAsByteString (intAsUint x)
  else byteAsByteString ((intAsUint (2^7 + x)) + 2^7)
```

#### Int16

Range varies from `-2^15` to `2^15-1`

```haskell
data Int16 = -2^15 | -2^15+1 | ... | 2^15-2 | 2^15-1
```

##### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Int16 -> Json
encodeJson x = intAsJson x
```

##### Binary

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

```haskell
encodeBinary :: Int16 -> ByteString
encodeBinary x = intAsByteStringBE x
```

```haskell
encodeBinary :: Int16 -> ByteString
encodeBinary x = intAsByteStringLE x
```

#### Int32

Range varies from `-2^31` to `2^31-1`

```haskell
data Int32 = -2^31 | -2^31+1 | ... | 2^31-2 | 2^31-1
```

##### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Int32 -> Json
encodeJson x = intAsJson x
```

##### Binary

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

```haskell
encodeBinary :: Int32 -> ByteString
encodeBinary x = intAsByteStringBE x
```

```haskell
encodeBinary :: Int32 -> ByteString
encodeBinary x = intAsByteStringLE x
```

#### Int64

Range varies from `-2^63` to `2^63-1`

```haskell
data Int64 = -2^63 | -2^63+1 | ... | 2^63-2 | 2^63-1
```

##### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Int64 -> Json
encodeJson x = intAsJson x
```

##### Binary

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

```haskell
encodeBinary :: Int64 -> ByteString
encodeBinary x = intAsByteStringBE x
```

```haskell
encodeBinary :: Int64 -> ByteString
encodeBinary x = intAsByteStringLE x
```

### Unsigned

#### Uint8

Range varies from `0` to `2^8-1`

```haskell
data Uint8 = 0 | 1 | ... | 2^8-2 | 2^8-1
```

##### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Uint8 -> Json
encodeJson x = uintAsJson x
```

##### Binary

```haskell
encodeBinary :: Uint8 -> ByteString
encodeBinary x = byteAsByteString x
```

#### Uint16

Range varies from `0` to `2^16-1`

```haskell
data Uint16 = 0 | 1 | ... | 2^16-2 | 2^16-1
```

##### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Uint16 -> Json
encodeJson x = uintAsJson x
```

##### Binary

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

```haskell
encodeBinary :: Uint16 -> ByteString
encodeBinary x = uintAsByteStringBE x
```

```haskell
encodeBinary :: Uint16 -> ByteString
encodeBinary x = uintAsByteStringLE x
```

#### Uint32

Range varies from `0` to `2^32-1`

```haskell
data Uint32 = 0 | 1 | ... | 2^32-2 | 2^32-1
```

##### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Uint32 -> Json
encodeJson x = uintAsJson x
```

##### Binary

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

```haskell
encodeBinary :: Uint32 -> ByteString
encodeBinary x = uintAsByteStringBE x
```

```haskell
encodeBinary :: Uint32 -> ByteString
encodeBinary x = uintAsByteStringLE x
```

#### Uint64

Range varies from `0` to `2^64-1`

```haskell
data Uint64 = 0 | 1 | ... | 2^64-2 | 2^64-1
```

##### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Uint64 -> Json
encodeJson x = uintAsJson x
```

##### Binary

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

```haskell
encodeBinary :: Uint64 -> ByteString
encodeBinary x = uintAsByteStringBE x
```

```haskell
encodeBinary :: Uint64 -> ByteString
encodeBinary x = uintAsByteStringLE x
```

## Floating Point

### Float32

A binary32 implementation of [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754)

#### JSON

Uses standard JSON Numbers

```haskell
encodeJson :: Float32 -> Json
encodeJson x = floatAsJson x
```

#### Binary

There are two byte encodings for any floating point number - big-endian or little-endian.

```haskell
encodeBinary :: Float32 -> ByteString
encodeBinary x = floatAsByteStringBE x
```

```haskell
encodeBinary :: Float32 -> ByteString
encodeBinary x = floatAsByteStringLE x
```

### Float64

A binary64 implementation of [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754)

#### JSON

Uses standard JSON Numbers

```haskell
encodeJson :: Float64 -> Json
encodeJson x = floatAsJson x
```

#### Binary

There are two byte encodings for any floating point number - big-endian or little-endian.

```haskell
encodeBinary :: Float64 -> ByteString
encodeBinary x = floatAsByteStringBE x
```

```haskell
encodeBinary :: Float64 -> ByteString
encodeBinary x = floatAsByteStringLE x
```

## UTF-8 String

All strings must be valid UTF-8 characters, especially with respect to surrogate codes between `0xD800` and
`0xDFFF` - with respect to [RFC 3629](https://en.wikipedia.org/wiki/UTF-8#Invalid_code_points). Conversion
a 'la CESU-8 may or may not be defined with this data type.

### JSON

Uses standard JSON Strings

```haskell
encodeJson :: String -> Json
encodeJson x = stringAsJson x
```

### Binary

Encodes to a ByteString as [standard UTF-8](https://en.wikipedia.org/wiki/UTF-8#Description).

```haskell
encodeBinary :: String -> ByteString
encodeBinary x = utf8AsByteString x
```
