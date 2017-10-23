# Largest pair sum in array
## Instruction
[Link](http://www.codewars.com/kata/556196a6091a7e7f58000018)

## Solution

```javascript
function pickMax(numbers) {
  max_idx = 0;
  for(i = 1; i != numbers.length; i++) {
    if (numbers[i] > numbers[max_idx]) {
      max_idx = i;
    }
  }

  return numbers.splice(max_idx, 1)[0];
}


function largestPairSum(numbers)
{
  return pickMax(numbers) + pickMax(numbers);
}
```

한번에 두개 뽑기가 귀찮아서, Max를 뽑아주는 함수를 하나 만들어서 두 번 불렀다. 짜면서 생각 못했던건 sort의 존재라고나 할까.

## Best Solution

```javascript
function largestPairSum(numbers){
  numbers.sort(function(a, b){ return b - a });
  return numbers[0] + numbers[1];
}
```

그냥 sort 호출했으면 되었을 것을.
그...그래도 max 두번 구하는게 빠를거라는 위안을 하며 마무리[...]
