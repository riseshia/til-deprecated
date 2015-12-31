# Pascal's Triangle #2
## Instruction
[Link](http://www.codewars.com/kata/52945ce49bb38560fe0001d9)

## Solution

```ruby
def pascal(depth)
  return [[1]] if depth == 1
  previous = pascal(depth-1)
  previous << gen_next(previous.last)
end

def gen_next(array)
  new_array = array.clone
  new_array << 0
  array.each_with_index do |i, idx|
    new_array[idx+1] += i
  end
  new_array
end
```

그냥 파스칼 삼각형 그리는 문제였는데, 문제가 어렵다기보다는 어떻게 하면 좀 보기 좋게 짤 수 있을까, 라는 고민을 많이 했다. 그래봐야 별로 보기 좋지는 않았지만. 특징이라고 한다면,

 * head-recursive
 * readability....?

이해하기 쉬운가는 둘째치고 딱히 잘짰다는 생각이 안드는 게, 대입이 많고, 분명 간결한 방법으로 더 줄일 수 있을거 같아보여서.

## Best Solution

```ruby
  a = [[1]]
  (n - 1).times do
    a << ([0] + a.last + [0]).each_cons(2).map { |a| a.reduce(:+) }
  end
  a
```

이런 종류의 문제들 답이 그렇듯, 이 문제도 재귀 안쓰는게 best solution.

그 와중에 발견한게, `each_cons`. ruby는 정말 별의 별게 다 stdlib에 있구나, 싶음. 물론 넣어주셔서 감사합니다. 아무튼 저걸로 2개씩 뽑는 코드가 간결해졌고, 앞뒤로 0 더해서 map 돌리는걸로 간단하게 해결.
