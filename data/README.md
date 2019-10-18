# Symbiotic Data

## Primitives

### Unit

```haskell
data Unit = Unit
```

#### JSON

Treats `Unit` as an empty string `""`

```haskell
encodeJson :: Unit -> Json
encodeJson Unit = stringAsJson ""
```

#### Binary

Stores it as the byte `0`

```haskell
encodeBinary :: Unit -> ByteString
encodeBinary Unit = byteAsByteString 0
```

### Boolean

```haskell
data Boolean = True | False
```

#### JSON

Uses standard JSON Boolean

```haskell
encodeJson :: Boolean -> Json
encodeJson x = case x of
  True -> true
  False -> false
```

#### Binary

Uses standard bytes `0` as false, `1` as true

```haskell
encodeBinary :: Boolean -> ByteString
encodeBinary x = case x of
  True -> byteAsByteString 1
  False -> byteAsByteString 0
```

### Integral

#### Signed

##### Int8

Range varies from `-2^7` to `2^7-1`

```haskell
data Int8 = -2^7 | -2^7+1 | ... | 2^7-2 | 2^7-1
```

###### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Int8 -> Json
encodeJson x = intAsJson x
```

###### Binary

`Int8`s are converted to `Uint8`s before storing as a byte - where the negative range is stored as
the upper values in the `Uint8`.

```haskell
encodeBinary :: Int8 -> ByteString
encodeBinary x =
  if x >= 0
  then byteAsByteString (intAsUint x)
  else byteAsByteString ((intAsUint (2^7 + x)) + 2^7)
```

##### Int16

Range varies from `-2^15` to `2^15-1`

```haskell
data Int16 = -2^15 | -2^15+1 | ... | 2^15-2 | 2^15-1
```

###### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Int16 -> Json
encodeJson x = intAsJson x
```

###### Binary

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

```haskell
encodeBinary :: Int16 -> ByteString
encodeBinary x = intAsByteStringBE x
```

```haskell
encodeBinary :: Int16 -> ByteString
encodeBinary x = intAsByteStringLE x
```

##### Int32

Range varies from `-2^31` to `2^31-1`

```haskell
data Int32 = -2^31 | -2^31+1 | ... | 2^31-2 | 2^31-1
```

###### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Int32 -> Json
encodeJson x = intAsJson x
```

###### Binary

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

```haskell
encodeBinary :: Int32 -> ByteString
encodeBinary x = intAsByteStringBE x
```

```haskell
encodeBinary :: Int32 -> ByteString
encodeBinary x = intAsByteStringLE x
```

##### Int64

Range varies from `-2^63` to `2^63-1`

```haskell
data Int64 = -2^63 | -2^63+1 | ... | 2^63-2 | 2^63-1
```

###### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Int64 -> Json
encodeJson x = intAsJson x
```

###### Binary

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

```haskell
encodeBinary :: Int64 -> ByteString
encodeBinary x = intAsByteStringBE x
```

```haskell
encodeBinary :: Int64 -> ByteString
encodeBinary x = intAsByteStringLE x
```

#### Unsigned

##### Uint8

Range varies from `0` to `2^8-1`

```haskell
data Uint8 = 0 | 1 | ... | 2^8-2 | 2^8-1
```

###### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Uint8 -> Json
encodeJson x = uintAsJson x
```

###### Binary

```haskell
encodeBinary :: Uint8 -> ByteString
encodeBinary x = byteAsByteString x
```

##### Uint16

Range varies from `0` to `2^16-1`

```haskell
data Uint16 = 0 | 1 | ... | 2^16-2 | 2^16-1
```

###### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Uint16 -> Json
encodeJson x = uintAsJson x
```

###### Binary

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

```haskell
encodeBinary :: Uint16 -> ByteString
encodeBinary x = uintAsByteStringBE x
```

```haskell
encodeBinary :: Uint16 -> ByteString
encodeBinary x = uintAsByteStringLE x
```

##### Uint32

Range varies from `0` to `2^32-1`

```haskell
data Uint32 = 0 | 1 | ... | 2^32-2 | 2^32-1
```

###### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Uint32 -> Json
encodeJson x = uintAsJson x
```

###### Binary

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

```haskell
encodeBinary :: Uint32 -> ByteString
encodeBinary x = uintAsByteStringBE x
```

```haskell
encodeBinary :: Uint32 -> ByteString
encodeBinary x = uintAsByteStringLE x
```

##### Uint64

Range varies from `0` to `2^64-1`

```haskell
data Uint64 = 0 | 1 | ... | 2^64-2 | 2^64-1
```

###### JSON

Uses standard JSON Integers

```haskell
encodeJson :: Uint64 -> Json
encodeJson x = uintAsJson x
```

###### Binary

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

```haskell
encodeBinary :: Uint64 -> ByteString
encodeBinary x = uintAsByteStringBE x
```

```haskell
encodeBinary :: Uint64 -> ByteString
encodeBinary x = uintAsByteStringLE x
```

> TODO Integers of arbitrary precision

### Floating Point

#### Float32

A binary32 implementation of [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754)

##### JSON

Uses standard JSON Numbers

```haskell
encodeJson :: Float32 -> Json
encodeJson x = floatAsJson x
```

##### Binary

There are two byte encodings for any floating point number - big-endian or little-endian.

```haskell
encodeBinary :: Float32 -> ByteString
encodeBinary x = floatAsByteStringBE x
```

```haskell
encodeBinary :: Float32 -> ByteString
encodeBinary x = floatAsByteStringLE x
```

#### Float64

A binary64 implementation of [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754)

##### JSON

Uses standard JSON Numbers

```haskell
encodeJson :: Float64 -> Json
encodeJson x = floatAsJson x
```

##### Binary

There are two byte encodings for any floating point number - big-endian or little-endian.

```haskell
encodeBinary :: Float64 -> ByteString
encodeBinary x = floatAsByteStringBE x
```

```haskell
encodeBinary :: Float64 -> ByteString
encodeBinary x = floatAsByteStringLE x
```

> TODO Scientific Numbers of Arbitrary Precision

### UTF-8 String

All strings must be valid UTF-8 characters, especially with respect to surrogate codes between `0xD800` and
`0xDFFF` - with respect to [RFC 3629](https://en.wikipedia.org/wiki/UTF-8##Invalid_code_points). Conversion
a 'la CESU-8 may or may not be defined with this data type.

#### JSON

Uses standard JSON Strings

```haskell
encodeJson :: String -> Json
encodeJson x = stringAsJson x
```

#### Binary

Encodes to a ByteString as [standard UTF-8](https://en.wikipedia.org/wiki/UTF-8##Description).

```haskell
encodeBinary :: String -> ByteString
encodeBinary x = utf8AsByteString x
```

## Casual

### DateTime

Can be represented internally as any "sane" date / time system.

#### JSON

Formatted as an [ISO 8601 String](https://en.wikipedia.org/wiki/ISO_8601)

```haskell
encodeJson :: DateTime -> Json
encodeJson x = stringAsJson (iso8601 x)
```

> - TODO Binary serialization implementation - minimal as possible, but not 64-bit reliant
> - TODO Date, Time, Interval (Date,Time,DateTime), Various Precision

### Email Address

Should be represented in a vaild Email Address format, as per [RFC 5322](https://en.wikipedia.org/wiki/Email_address#Syntax)

#### JSON

Formatted as the string representation

```haskell
encodeJson :: EmailAddress -> Json
encodeJson x = stringAsJson (emailAddressAsString x)
```

> - TODO Binary serialization that takes advantage of pre-processed string
> - TODO International Email Addresses a 'la https://en.wikipedia.org/wiki/International_email


## Primitive Composites

### Collections

#### Array

A size-indexed array of homogeneous data.

```haskell
data Array (n :: Nat) a where
  Nil :: Array 0 a
  Cons :: a -> Array n a -> Array (n + 1) a
```

##### JSON

Uses standard JSON Arrays

```haskell
encodeJson :: Array n Json -> Json
encodeJson x = arrayAsJson x
```

##### Binary

Ommits a size parameter, because the size is encoded in the type signature.

```haskell
encodeBinary :: Array n ByteString -> ByteString
encodeBinary x = case x of
  Nil -> emptyByteString
  Cons y ys -> y ++ (encodeBinary ys)
```

#### Vector32

A dynamically sized array that limits the max size to `2^32` elements

##### JSON

Uses standard JSON Arrays

```haskell
encodeJson :: Vector32 Json -> Json
encodeJson x = arrayAsJson x
```

##### Binary

Prefixes the length of the array as a 32-bit unsigned integer, big-endian, before concatenating all contents.

```haskell
encodeBinary :: Vector32 ByteString -> ByteString
encodeBinary x = (uintAsByteStringBE l) ++ (concatVector32 x)
```

#### Vector64

A dynamically sized array that limits the max size to `2^64` elements

##### JSON

Uses standard JSON Arrays

```haskell
encodeJson :: Vector64 Json -> Json
encodeJson x = arrayAsJson x
```

##### Binary

Prefixes the length of the array as a 64-bit unsigned integer, big-endian, before concatenating all contents.

```haskell
encodeBinary :: Vector64 ByteString -> ByteString
encodeBinary x = (uintAsByteStringBE l) ++ (concatVector64 x)
```

### Maybe

Standard option type

```haskell
data Maybe a = Nothing | Maybe a
```

#### JSON

Uses standard JSON `null` if `Nothing`, otherwise just use its JSON - leverages backtracking

```haskell
encodeJson :: Maybe Json -> Json
encodeJson x = case x of
  Nothing -> nullJson
  Just y -> y
```

#### Binary

Use a prefix byte flag to avoid backtracking

```haskell
encodeBinary :: Maybe ByteString -> ByteString
encodeBinary x = case x of
  Nothing -> byteAsByteString 0
  Just y -> (byteAsByteString 1) ++ y
```

### Tuple

```haskell
data Tuple a b = Tuple a b
```

#### JSON

Uses a standard JSON Array to hold the two elements

```haskell
encodeJson :: Tuple Json Json -> Json
encodeJson (Tuple x y) = [x,y]
```

#### Binary

Is equivalent to an array of size 2, therefore avoids a size prefix

```haskell
encodeBinary :: Tuple ByteString ByteString -> ByteString
encodeBinary (Tuple x y) = x ++ y
```

### Either

```haskell
data Either a b = Left a | Right b
```

#### JSON

Flags each case with a unique object key

```haskell
encodeJson :: Either Json Json -> Json
encodeJson x = case x of
  Left y -> {"l": y}
  Right z -> {"r": z}
```

#### Binary

Flags each case with a byte prefix

```haskell
encodeBinary :: Either Json Json -> Json
encodeBinary x = case x of
  Left y -> (byteAsByteString 0) ++ y
  Right z -> (byteAsByteString 1) ++ z
```
