# 라우팅 속도와 Constraint와의 상관 관계
## Why?

```
애플리케이션에서 RESTful한 라우팅을 사용하고 있다면 각각에 적절한 :only나 :except 옵션을 사용해서 정말로 필요한 라우팅만을 생성하여 메모리를 절약하고, 라우팅 속도 향상을 꾀할 수 있습니다.
```
[출처](http://guides.rorlab.org/routing.html)

번역 중에 이 내용을 보고, 얼마정도로 유의미하게 성능에 영향을 미치는지 궁금해져서 실험을 해봤습니다.

## Environment
* MBPR 15'
* Rails 4.2.4
* Ruby 2.2.3
* Active 모델 100개 -> 라우팅 2~100개

## Test code

라우팅 선언은 다음의 3가지 중 하나를 사용했습니다.

```ruby
resources :route00s                # case1

resources :route00s,
  only: :index                     # case2

resources :route00s,
  except: [:show, :edit, :destroy] # case3
```

사용하는 라우팅 전체에 케이스별로 동일한 제약 조건을 주었습니다.

```ruby
Benchmark.ips do |x|
  x.time = 50
  x.report("First Index") {
    Rails.application.routes.recognize_path('/route00s')
  }

  x.report("Last Index") {
    Rails.application.routes.recognize_path('/route99s')
  }
  x.compare!
end
```

테스트는 위와 같이 작성했습니다. 실제 URL에서 라우팅 된 컨트롤러/액션을 찾아주는 메소드를 사용해서, 이 코드를 기준으로 라우팅에서 여러가지 조건을 변화시켰는데요.

* resource 갯수가 라우팅에 미치는 영향(2개, 100개)
* 탐색 라우팅 목록이 미치는 영향(각 조건 하에서 첫번째 리소스와 마지막 리소스를 매번 측정)
* contraint 조건이 라우팅에 미치는 영향(index만을 only, except 각각을 사용해 측정)

## Result(i/s)
| Routing Count | Case | First Index | Last Index |
| ------------- | ---- | ----------- | ---------- |
| 2             | case1 | 15.671k (± 4.4%) | 15.627k (± 5.5%) |
| 2             | case2 | 16.547k (± 5.4%) | 16.593k (± 4.8%) |
| 2             | case3 | 16.515k (± 3.7%) | 16.489k (± 3.8%) |
| 100           | case1 | 15.970k (±11.0%) | 16.374k (± 9.6%) |
| 100           | case2 | 15.603k (± 7.3%) | 16.740k (± 4.1%) |
| 100           | case3 | 15.729k (± 4.5%) | 15.753k (± 4.1%) |

## Conclusion
올바른 테스트를 했다는 전제하에...

* 모델수가 미묘하게 성능에 영향을 줍니다.
* only, except는 성능에 큰 영향을 주지 않는 것처럼 보입니다.
* routing 선언 순서가 오히려 성능에 큰 영향을 줍니다. --그래봐야...--

-> 성능 상으로 크게 성능 향상을 주지는 않지만, 보안을 위해서라도 쓰는 것이 좋습니다.

-> 너무 빠른게 문제인건지, 아니면 관련 최적화가 덜 되서인건지, 또는 다른 이유가 있는 것인지, 라우팅 관련 소스코드를 볼 필요가 있을 듯합니다.
