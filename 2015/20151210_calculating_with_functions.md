# Calculating with Functions
## Instruction
[Link](http://www.codewars.com/kata/525f3eda17c7cd9f9e000b39/)

## Solution

```ruby
def common i, expr
  return i unless expr
  return calculate(i, expr.split(' ').first, expr.split(' ').last.to_i)
end

def zero expr=nil
  common 0, expr
end
def one expr=nil
  common 1, expr
end
def two expr=nil
  common 2, expr
end
def three expr=nil
  common 3, expr
end
def four expr=nil
  common 4, expr
end
def five expr=nil
  common 5, expr
end
def six expr=nil
  common 6, expr
end
def seven expr=nil
  common 7, expr
end
def eight expr=nil
  common 8, expr
end
def nine expr=nil
  common 9, expr
end
def plus params
 return "+ #{params}"
end
def minus params
 return "- #{params}"
end
def times params
 return "* #{params}"
end
def divided_by params
 return "/ #{params}"
end

def calculate a, expr, b
  case expr
  when '*' then return a * b
  when '+' then return a + b
  when '-' then return a - b
  when '/' then return a / b.to_f
  end
end
```

메타 코드를 작성하면 좀 편하겠지, 라는 생각은 했는데, 그쪽 문법을 완벽하게 아는 것은 아니라서 그냥 가볍게 풀어봤음. 그러자...

## Best Solution

```ruby
class Object
  %w[zero one two three four five six seven eight nine].each_with_index do |name, n|
    define_method(name) do |args = nil|
      args ? n.send(*args) : n.to_f
    end
  end
  
  %w[plus + minus - times * divided_by /].each_slice(2) do |name, symbol|
    define_method(name) do |n|
      [symbol, n]
    end
  end
end
```

define_method의 적절한 활용과, 기본 클래스를 수정할 수 있는 Ruby의 적절한 조합.
웃음만....

