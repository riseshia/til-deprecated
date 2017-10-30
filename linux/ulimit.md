# ulimit

## 찾아보게된 계기

https://qiita.com/iorionda/items/4d301e5d5b115a018ba8

와 동일한 에러가 발생해서.

## 설명

쉘 명령이 사용할 수 있는 자원에 대해서 get/set할 수 있음.

`ulimit [-acdflmnpstuvHS] [N]`

N이 지정되면 set, 없다면 get으로 동작.

설정할 수 있는 값은 os에 따라서 다른 모양. 예를 들어 osx라면 

```bash
> til ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
file size               (blocks, -f) unlimited
max locked memory       (kbytes, -l) unlimited
max memory size         (kbytes, -m) unlimited
open files                      (-n) 256
pipe size            (512 bytes, -p) 1
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 709
virtual memory          (kbytes, -v) unlimited
```

이런 느낌. `-a`로 설정 가능한 모든 값/옵션을 확인할 수 있으니 참고합시다.
hard limit, soft limit도 설정 가능은 한데, 지금 딱히 고려할 필요는 없을거 같다.
