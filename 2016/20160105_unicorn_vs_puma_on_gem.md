# Capistrano-nginx-unicorn vs Capistrano-puma

딱히 여기서 성능을 비교하고 싶은 마음은 없고 공식은 아니지만 각각 capistrano를 편하게 사용하기 위한, 젬이 있는데, 최근에 puma로 서버를 배포해보면서 느꼈던 부분을 간단하게 정리해보고자 함.

## Capistrano-nginx-unicorn(cnu)
[Link](https://github.com/capistrano-plugins/capistrano-unicorn-nginx)

### 장점
 * ssh로 작업을 할 필요가 없음. setup이라는 태스크를 통해서 nginx, unicorn, init.d 실행 스크립트까지 한방에 세팅해주는 편의성이 눈부심

### 단점
 * unicorn, nginx 설정 파일 커스텀하는 것이 꽤 불편한 편. 기본값만 가져다 쓸거라면 큰 문제가 없지만, ssl을 추가한다던가, 그 이외의 설정을 추가하는 상황이 되면 꽤 곤란한 상황이 발생할 수 있음.
 * CentOS에서의 동작을 보증하지 못함. OS Detect랑 관련된 스크립트 이슈가 있었던 걸로 기억하는데, PR은 있는데 아직 처리가 되지 않은 상태.

## Capistrano-puma
[Link](https://github.com/seuros/capistrano-puma)
### 장점
 * unicorn 젬에서는 볼 수 없던 강력한 설정 파일 생성기능 -> 그냥 템플릿을 꺼내주고 맘대로 커스텀 할 수 있게 해줌. 완전 편함 [..]

### 단점
 * 초기 setup시에 config 파일을 올리는 작업이나, shared, log 폴더등을 수작업으로 만들어줘야 한다는 면이 귀찮음. 이 부분은 모든 앱 공통이라 자동화해줘도 될법함에도 불구하고, 안해주더라...
 * 실행 스크립트를 생성해주지 않음. 다시 말해서 완벽한 환경 설정 & 실행 명령을 기억하고 있지 않는 이상 당신에게 남은 유일한 선택지는 kill process 또는 deploy뿐 [....]
