# Return substring instance count - 2
## Instruction
[Link](http://www.codewars.com/kata/52190daefe9c702a460003dd)

## Solution

```ruby
def search_substr( fullText, searchText, allowOverlap = true )
  return 0 if searchText.empty?
  matched = (fullText =~ Regexp.new(searchText))
  return 0 unless matched
  next_idx = (allowOverlap ? 1 : searchText.size) + matched
  
  1 + search_substr(fullText[next_idx..-1], searchText, allowOverlap)
end
```

문제를 풀때 고려했던건 allowOverlap의 예외처리. 이게 없으면 map/inject 등으로 보기 좋게 처리할 수 있었는데, 이거 때문에 그게 안되더라...고 고민하며 재귀처리를 했는데,

## Best Solution

```ruby
def search_substr( fullText, searchText, allowOverlap = true )
  if searchText == ''
    0
  else
    fullText.scan(allowOverlap ? Regexp.new("(?=(#{searchText}))") : searchText).size
  end
end
```

생각해보니 scan이 있었습니다. 왜 이 생각을 못했을까[...]
지금까지 내 고민은 무엇이었나, 하는 생각도 들고. 앞으로는 scan을 적극 활용하자고 다짐했습니다.
