# Data Merging하기

모 님이 엑셀로 이거 어떻게 하죠? 라는 질문을 듣고 처음에는 차분만 만들면 되지, 이러고 있었습니다. 문제는 이랬습니다.

## Question

* 2개의 식별자 목록 + 식별자에 따른 추가정보가 있음
* 식별자1, 식별자2, 추가정보 순서의 데이터.
* 추가정보는 같은 행에 있는 식별자1의 데이터.
* 식별자1 목록과 식별자2의 목록을 하나로 머징하는 작업.
* 식별자1 목록에 없고, 식별자2에 있으면 그 식별자를 식별자 1에 추가
* 식별자1 목록에 있고, 식별자2에 없으면 식별자2를 아래로 밀어내기

이제와서 생각해보자면, 식별자2 목록을 아예 별도로 빼내고, 각각 테스트를 하면서 없으면 행을 하나 추가하고, 있으면 그냥 없애버리고 하는 방향으로 했어도 되는것이 아닌가.

아무튼 이런 작업인데 원래는 저 병합작업을 위해서 틀린 부분만 찾아달라는 요청이었는데, 엑셀에서 하면 수작업이 될거 같아 그냥 csv -> ruby에서 머징 -> csv -> 엑셀파일로 작업했습니다.

최종적으로 시간을 더 많이 쓴것 같지만...

## Solution

```ruby
require 'csv'

def equal? cols
  cols[1].first == cols[4].first && cols[2].first == cols[3].first
end

def flush_row cols
  cols.map do |col|
    col.shift
  end
end

def flush_row_with_text cols, text
  new_row = cols.map do |col|
    col.shift
  end

  raise "Another text is here!" if new_row.first
  new_row[0] = text
  new_row
end

def flush_right_col cols
  cols.map.with_index do |col, idx|
    if idx == 3 || idx == 4
      nil
    else
      col.shift
    end
  end
end

def flush_left_col cols
  new_row = Array.new(23)
  new_row[1] = cols[4].first
  new_row[2] = cols[3].first
  new_row[3] = cols[3].first
  new_row[4] = cols[4].first
  cols[3].shift
  cols[4].shift
  new_row
end

cols = Array.new(23) { [] }
CSV.foreach("raw.csv") do |row|
  row.each_with_index do |val, idx|
    cols[idx].push val
  end
end

CSV.open("trans.csv", "wb") do |csv|
  while !cols.first.empty?
    if cols[1].first == "관리번호"
      csv << flush_row(cols)
    elsif equal?(cols) || cols[2].first.nil? || cols[3].first.nil?
      csv << flush_row(cols)
    elsif cols[2].first < cols[3].first
      # 오른쪽 밀기
      csv << flush_right_col(cols)
    elsif cols[2].first > cols[3].first
      # 왼쪽 밀기
      csv << flush_left_col(cols)
    elsif cols[1].first < cols[4].first
      csv << flush_row_with_text(cols, "번호다름")
    elsif cols[1].first > cols[4].first
      csv << flush_row_with_text(cols, "번호다름")
    else
      puts flush_row(cols).to_s
      raise "Not handling"
    end
  end
end
```

처음에 실패한 코드는 생략하고, csv를 각 열별로 배열을 만들고 식별자의 상태에 따라서 필요한 부분만을 꺼내서 새 행을 만들었음. 이런 식으로 짜서 얻을 수 있는 장점은 식별자1이나 2쪽에서 여러개가 결락되어 있을 경우에 별도의 스택을 관리할 필요가 없다는 점?

## 고찰
* 좀 더 깔끔하게 만들 수 있을 것 같은데...
* 문제 정의를 좀 더 명확히 이해할 것. 이 부분만 잘했어도 좀 더 빠른 시간에 짤 수 있었음.
* 문제를 푸는 사람도 암묵적으로 이해하고 있지만 남에게 설명 못하는 부분도 많다. 이런 포인트를 잡아내는 것이 관찰력...!
