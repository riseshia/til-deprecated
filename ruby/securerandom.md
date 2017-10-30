# Securerandom

[code](https://github.com/ruby/ruby/blob/7ed3aae2feb91c584c5402dd6f63c145acd1f033/lib/securerandom.rb)

안전한 난수 생성 인터페이스.

이 라이브러리는 HTTP 쿠키의 세션 키 등을 생성하는데 적합한 난수 생성기들의 인터페이스입니다.
다음과 같은 난수 생성기를 지원합니다.

- openssl
- /dev/urandom
- Win32

## API

사용 가능한 난수 생성기가 없다면, NotImplementedError가 발생한다.

### `base64(n=nil)`

무작위의 base64 문자열을 생성. `n`은 생성될 바이트 길이를 지정. `n`이 nil이면 16이라고 가정되며 이후로 더 커질 수 있으니 주의.
결과는 `A-Za-z0-9+/=`를 포함한다.

```ruby
p SecureRandom.base64 #=> "/2BuBuLf3+WfSKyQbRcc/A=="
p SecureRandom.base64 #=> "6BbW0pxO0YENxn38HMUbcQ=="
```

자세한 사양에 대해서는 RFC 3548을 참조.

### `urlsafe_base64(n=nil, padding=false)

무작위의 url에 사용 가능한 base64 문자열을 생성. `n`은 생성될 바이트 길이를 지정. `n`이 nil이면 16이라고 가정되며 이후로 더 커질 수 있으니 주의.
결과는 `A-Za-z0-9+/`를 포함한다. 패딩인 `=`는 url에서 delimiter로 사용되기 때문에 기본값은 false. 패딩이 true라면 `=`도 사용된다.

```ruby
p SecureRandom.urlsafe_base64 #=> "b4GOKm4pOYU_-BOXcrUGDg"
p SecureRandom.urlsafe_base64 #=> "UZLdOkzop70Ddx-IJR0ABg"

p SecureRandom.urlsafe_base64(nil, true) #=> "i0XQ-7gglIsHGV2_BNPrdQ=="
p SecureRandom.urlsafe_base64(nil, true) #=> "-M8rLhr7JEpJlqFGUMmOxg=="
```

자세한 사양에 대해서는 RFC 3548을 참조.

### `hex(n=nil)`

무작위의 16진수 문자열을 생성. `n`은 생성될 바이트 길이를 지정. `n`이 nil이면 16이라고 가정되며 이후로 더 커질 수 있으니 주의.
`a-z0-9`를 사용한다.

```ruby
p SecureRandom.hex #=> "eb693ec8252cd630102fd0d0fb7c3485"
p SecureRandom.hex #=> "91dc3bfb4de5b11d029d376634589b61"
```

### `random_bytes(n=nil)`

무작위의 바이너리 문자열을 생성. `n`은 생성될 바이트 길이를 지정. `n`이 nil이면 16이라고 가정되며 이후로 더 커질 수 있으니 주의.

바이트의 범위는 'x00' - 'xff'

```ruby
p SecureRandom.random_bytes #=> "\xD8\\\xE0\xF4\r\xB2\xFC*WM\xFF\x83\x18\xF45\xB6"
p SecureRandom.random_bytes #=> "m\xDC\xFC/\a\x00Uf\xB2\xB2P\xBD\xFF6S\x97"
```

### `uuid`

v4 UUID를 생성. 결과는 임의의 122비트를 반환.

```ruby
p SecureRandom.uuid #=> "2d931510-d99f-494a-8c67-87feb05e1594"
p SecureRandom.uuid #=> "bad85eb9-0713-4da7-8d36-07a8e4b00eab"
p SecureRandom.uuid #=> "62936e70-1815-439b-bf89-8492855a7e6b"
```

자세한 사양에 대해서는 RFC 4122를 참조.
