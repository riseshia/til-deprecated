# Upgrade to Jekyll 3.0

어제 급거 발표된 [깃헙 페이지의 버전업](https://github.com/blog/2100-github-pages-now-faster-and-simpler-with-jekyll-3-0). 호환성 이슈가 많다고 해서 긴장하고 있었는데, 막상 해보니 생각보다 별 이슈가 없었습니다. 그래서 했던 작업 정리하는김에 간단하게 주요 변경점 요약 및 어제 깃헙 페이지 3개를 사전 패치할때 있었던 몇몇 트러블 슈팅을 정리했습니다.

## 주요 변경점

현재까지는 안내사항이며, 이하의 내용은 2016년 5월 1일부터 적용됩니다.

* 깃헙 페이지가 **[kramdown](http://kramdown.gettalong.org)이라는 마크다운 엔진만을 지원하게 됩니다.**
* Syntax-highlighter를 **[Rouge](https://github.com/jneen/rouge)만 지원하게 변경됩니다.**
* relative_permalinks 옵션이 제거됩니다.
* [Textile](http://redcloth.org/textile)이 고인이 되셨습니다.
* `jekyll-paginate`가 기본 의존성에서 제외되었습니다.

좀 더 자세한 설명은 [Jekyll 3.0 Upgrade Guide](http://jekyllrb.com/docs/upgrading/2-to-3/)를 참고하면 좋습니다.

## How-to

### Change to Kradown

기존에 지원하던 Redcarpet, Rdiscount와 호환 가능하므로 그대로 넘어가시면 됩니다. 지금 당장 넘어가고 싶으시다면 설정(`_config.yml`)에서 명시적으로 사용할 엔진을 지정해주세요.

```yml
kramdown:
  input: GFM
```

collection 관련한 이슈가 발생한다면 위에 있는 Upgrade Guide를 참고하세요.

### Syntax-highlighter

pygment를 사용하던 당신! 걱정하실 필요 없습니다. Liquid Tag(`{ % highlight % }`)를 사용했다구요? 그래도 문제는 없습니다. 위에서 kramdown을 성공적으로 적용했다면 아무 문제 없을겁니다.

### Textile??

축하합니다. 전부 GFM으로 갈아엎어주세요. XD

수작업이 귀찮다면 Pandoc이라는 컨버터를 시도해보셔도 좋습니다. [온라인](http://pandoc.org/try/)에서 사용해볼 수 있으니, 밑져도 본전이라는 느낌으로 시도해주세요.

### relative_permalinks

이전에 상대주소를 사용하는 블로그를 만드셨다면, 앞으로는 강제적으로 전부 절대경로로 생성하게 될겁니다. 이 부분에서 특별한 이슈가 발생할것 같지는 않지만, 혹시라도 링크에서 문제가 생긴다면 이 부분을 의심해주세요.

### Dropped dependencies

이번에 기본에서 빠진 의존성들이 몇가지 있는데 이들은 `_config.yml`에 다음과 같이 추가해주세요.

```yml
gems: [jekyll-paginate]
```

저는 페이징 잼만 사용하고 있어서 이것만 추가했습니다.

## Trouble Shooting

### 새 글을 작성했는데 생성이 안돼요.

3.0에서는 `--future` 플래그의 기본값이 `false`으로 변경되었습니다. 아마 이 트랩에 걸리신 경우, 각 포스트의 생성시간이 한국 로컬 시간으로 되어있을 가능성이 높습니다. 포스트의 생성 시각을 utc로 변경해주세요. 예를 들어, 이렇게 말이죠.

```
---
layout: post
title: "Upgrade to Jekyll 3.0"
date: 2016-02-03 08:30:17
categories:
---
```

```
---
layout: post
title: "Upgrade to Jekyll 3.0"
date: 2016-02-02 23:30:17
categories:
---
```

별도의 포스트 생성 태스크를 사용하고 계신다면 그 코드도 이에 맞추어 변경해주세요.

### 로컬에서 잘 보이는데요?

로컬에서 구 버전을 사용하고 계실 가능성이 높습니다. 번들러를 사용해서 실제 깃헙 페이지와 동일한 환경을 만들어주세요. 번들러를 모르신다구요? ok. 여기부터 시작합시다.

이하, 깃헙페이지를 쓰고 계시므로 최소한 루비가 깔려있다고 가정합니다. 이왕이면 2.x로요.

```bash
ruby -v
```

를 사용해서 버전을 확인해주세요. 이보다 버전이 낮을 가능성은 한없이 낮지만 혹여나 낮으시거나, 없다면 새로 설치하세요(Jekyll 3.0의 최소 요구 조건이 Ruby 2.0입니다).

블로그 폴더 최상단에 `Gemfile`이라는 파일을 만들고 깃헙 페이지에서 요구하는 의존성 정보를 포함하는 잼을 추가해주세요.

```ruby
source 'https://rubygems.org/'

gem 'github-pages'
```

다음으로 프로젝트 최상단 폴더에서 의존성을 설치!

```bash
bundle install
```

잠시 기다리시면 루비가 열심히 의존성을 찾아서 설치해줍니다.

이걸로 끝입니다. 앞으로는 방금 설치한 의존성들은 해당 프로젝트에서만 유효하기 때문에,

```bash
bundle exec jekyll serve --watch
```

이런 식으로 `bundle exec`라는 명령을 통해서 실행해주셔야합니다.

좀 더 자세히 알고 싶으시다면 [Bundler](http://ruby-korea.github.io/bundler-site/)를 참고하세요. 많은 분들이 도와주셔서 유지되고 있는 한글판입니다. :)

### Gemfile.lock은 뭐죠?

번들러가 `Gemfile`이 가지고 있는 정보를 토대로 실제로 이 프로젝트에서 사용할 잼들의 의존성 및 사용하는 버전에 대한 정보를 저장하고 있는 파일입니다. 이 두 파일은 개발환경에서만 사용됩니다만, 현재 사용하고 있는 의존성을 확실히 명시하기 위해서라도 저장소에서 관리하는걸 추천드립니다.

### Jekyll 기동 속도가 너무 느려요.

이건 업그레이드랑 관계없는[..] 이야기인데, 대단하십니다. 존경합니다.

Jekyll은 serve 명령을 실행할 때마다 새롭게 html을 생성합니다. 이 말인즉슨, '생성할 html 파일이 너무 많다 -> 블로깅을 열심히 하셨다.' 라는 의미가 되겠습니다. 그런 당신을 위해서 `--skip-initial-build` 옵션이 준비되어 있습니다. 이름만 봐도 알 수 있겠지만 처음 serve 시에 미리 html 파일을 생성하지 않도록 해주는 옵션입니다.
