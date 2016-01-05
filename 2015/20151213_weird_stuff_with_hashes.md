# Hash의 이상한 동작

사실 제 생각에는 그렇게 이상한 동작은 아닙니다. 하지만 사람들은 여기에 대해서 잘 모르니 여기에 대해 한번 포스트를 올려보기로 했어요.

## Hashes dup string keys
해시에서 문자열 키를 할당할 때, 해시는 문자열을 복사하고 그것을 'freeze'합니다. 다음 예제를 보시죠.

```ruby
>> x = 'string'
=> "string"
>> y = {}
=> {}
>> y[x] = :value
=> :value
>> { x => y.keys.first }
=> {"string"=>"string"}
>> { x.object_id => y.keys.first.object_id }
=> {70157126548460=>70157126526760} # object ids are different
>> { x.frozen? => y.keys.first.frozen? }
=> {false=>true} # frozen value is different
```

이런 현상을 피하기 위해서는 해시에 넣기 전에 'freeze'하면 됩니다.

```ruby
>> x = 'string'
=> "string"
>> x.freeze
=> "string"
>> y = {}
=> {}
>> y[x] = :value
=> :value
>> { x.object_id => y.keys.first.object_id }
=> {70157126414020=>70157126414020} # note that both object ids are the same
```

## Why is this important?
저는 @eileencodes에서 Rails 에서의 통합 테스트의 성능을 향상시키기 위한 일을 하고 있습니다. 그런데 테스트 중 가비지 컬렉터가 엄청난 시간을 소모하고 있다는 점을 발견했습니다. 우리는 메모리 할당이 어디서 발생하는 지를 찾기 시작했고, 많은 문자열들이 해시의 헤더에서 할당되고 있다는 점을 발견했습니다.

이 문제를 부분적으로 해결하려면 해시의 헤더에서 문자열 키를 삽입하기 전에 'freeze'하는 것을 생각해 볼 수 있습니다. 이 방법은 할당양을 많이 줄일 수 있습니다. 보통 코드에서는 문자열을 '소문자'로 변환해서 할당하고, 소문자로 변환한 것을 해시에 집어 넣곤 합니다.

해시의 키로 문자열을 사용하면 애플리케이션에서 쓸모없는 객체 할당의 원인이 될 가능성이 높습니다. 그렇다고 모든 문자열을 'freeze'하지는 마세요. 우선은 앱의 어디에서 객체 할당이 발생하는지를 정확하게 파악할 필요가 있고(제가 다음 포스팅에서 쓸 주제이기도 합니다), 필요한 곳에만 잘 사용해야합니다.

## Why does the Hash dup strings?
저는 몇가지 이유가 있다고 생각합니다.

첫번째로 해시에 삽입한 이후에 문자열이 변경하게 되면, 해시에 삽입한 키값 역시 변경될겁니다. 예를 들어보면,

```ruby
>> x = 'string'
=> "string"
>> y = {}
=> {}
>> y[x] = :value
=> :value
>> x.sub!(/r/, '')
=> "sting"
>> y.key? x
=> false
>> { y.keys => x }
=> {["string"]=>"sting"}
```

제 생각에는 만약 다른 곳에서 객체를 변경했을 때 그것이 해시에 영향을 미친다면 꽤나 당황스러울겁니다.
그리고 또다른 기술적인 이유로는 아마 문자열이 변경될 경우, 그 키의 해시값 역시 변경되기 때문이라고 생각합니다. 다시 말해, 해시가 같은 키를 찾기 위해서 다시 새로 해싱작업을 수행해야 한다는 것을 의미하기도 하죠.

```ruby
>> x.hash
=> 4078764570319932244
>> x.sub!(/r/, '')
=> "sting"
>> x.hash
=> -1518905602476999392
```

위에서 문자열이 변경되면 해시값 역시 바뀐다는 것을 알 수 있습니다.

## Hash value, what does that have to do with anything?
만약 해시 키의 해시값을 바꾼다면, 그 키값은 해시에서 잘못된 위치에 존재하게 됩니다. 이 설명은 코드를 보시면 좀 더 명확하게 이해되실겁니다.

아마 해시 키를 커스텀으로 만들기 위해서 필요한 함수가 2개 있다는 것을 알고 계실텐데요. 하나는 'hash'이고, 나머지 하나는 'eql?'죠. 전자는 해시의 어느 위치에 객체를 저장할지를 알려주고, 후자는 해시값 충돌이 발생하는지를 확인할 수 있는 함수입니다.

해시값을 바꿀수 있는 객체를 만들고, 해시에 삽입한 이후에 그 객체가 어떻게 행동하는지를 살펴보죠.

```ruby
class Foo
  attr_accessor :hash
end

x = Foo.new
x.hash = 10

hash = {}
hash[x] = :hello

p hash.key?(x)          # => true
p hash.keys.include?(x) # => true

x.hash = 11 # change the hash value

p hash.key?(x)          # => false
p hash.keys.include?(x) # => true

hash.rehash

p hash.key?(x)          # => true
p hash.keys.include?(x) # => true
```

우선 x를 해시의 키로 만들었고, keys 에서도 확인할 수 있습니다. 그 다음 해시값을 변경하자, 'Hash#key?'는 'false'를 반환하지만, 'Array#include?'는 'true'를 반환합니다.

'Hash#key?'가 'false'를 반환하는 이유는 해시 내에서 객체를 찾기 위해서 hash 라는 값을 사용하고 있기 때문입니다. 우리가 객체를 처음 해시에 삽입했을 때에는 '10'이라고 적힌 자리에 위치하게 됩니다. 하지만 그 이후에 우리가 해시값을 '11'로 변경하고 나면, 해시는 객체를 '11'이라고 적힌 자리에서 찾게 됩니다. 잘못된 자리죠!

이를 해결하기 위해서는 'rehash' 함수를 호출해야할 필요가 있습니다. 이 'rehash' 함수는 키들의 위치 계산을 다시 수행해줍니다. 이제 '10'이라는 자리에 위치하는 객체는 '11'이라는 자리로 움직이기 때문에 올바른 위치에 위치하게 되고, 'Hash#key?'는 정상적으로 동작하게 됩니다.

만약 문자열을 변경할때마다 이와 같이 rehash를 반복해서 호출한다면 화날거 같지 않나요? 저는 아마 이것이 해시가 문자열을 복사하고 'freeze'하는 이유라고 생각합니다.

## What is the lesson?
해시 키의 해시값을 변경하지 마세요! 아니면 골아프실겁니다.

어쨌든, 저는 이것이 이상하거나 잘못된 동작이라고 생각하지 않으며, 우리가 매일 고민해야할 문제라고도 생각하지 않습니다. 아마 저는 이 포스팅의 제목을 "해시와 함께 자주 하지 않는 일들" 이라고 지었어야 했는지도 모르죠.

읽어주셔서 감사합니다!!! <3<3<3<3<3

## Performance P.S. (is that a P.P.S.?)
재미있는 것은 'Hash[]'는 rehash를 해주지 않는 반면, 'Hash#dup'는 새로 해시를 생성할때 'rehash'호출한다는 점입니다.

```ruby
class Foo
  attr_accessor :hash
end

x = Foo.new
x.hash = 10

hash = {}
hash[x] = :hello

p hash.key?(x)          # => true

x.hash = 11 # change the hash value

p hash.key?(x)          # => false
p hash.dup.key?(x)      # => true
p Hash[hash].key?(x)    # => false
```

한마디로 정리하자면 해시를 복사할 경우에 'Hash[]'가 'Hash#dup'보다 빠르다는 점입니다. 성능을 측정하기 위한 벤치마크를 보시죠.

```ruby
require 'benchmark/ips'

hash = Hash[*('a'..'z').to_a]
Benchmark.ips do |x|
  x.report("Hash#dup") do
    hash.dup
  end

  x.report("Hash[]") do
    Hash[hash]
  end
end
```

제 컴퓨터에서의 결과는 다음과 같습니다.

```ruby
Calculating -------------------------------------
            Hash#dup     7.705k i/100ms
              Hash[]    15.978k i/100ms
-------------------------------------------------
            Hash#dup     93.648k (± 4.9%) i/s -    470.005k
              Hash[]    230.497k (±11.2%) i/s -      1.150M
```

그러면 우리는 'Hash[]'로 갈아타야하는 걸까요?

아뇨. '앱의 벤치마킹 결과, 그것이 문제일 경우일 때만'입니다. 제발, 제발, 제발, 이게 빨라보인다는 이유로 코드에 있는 모든 'Hash#dup'를 'Hash[]'로 바꾸지 마세요. 앱의 성능을 우선 확인하세요. 하지만 이미 알고 있다면, 그게 바로 이 섹션을 읽고 있는 이유이겠지요. ;-)

## Etc
Ruby 2.3.0 preview에서는 리터럴 문자열을 기본적으로 freeze하는 매직 코멘트가 추가되었습니다.

## Original Post
이 글은 [Tender Love Making의 글](http://tenderlovemaking.com/2015/02/11/weird-stuff-with-hashes.html)를 허가받고 번역했습니다.
Thanks [Aaron](https://twitter.com/tenderlove)!!
