# update-alternatives

동일하거나 비슷한 기능을 가지는 여러 프로그램을 한 시스템에 설치해야하는 경우가 있음.
예를 들어, 많은 시스템들은 여러 텍스트 편집기를 동시에 설치하고 있음. 이는 시스템의 사용자에게 원한다면 서로 다른 에디터를 사용할 수 있는 선택지를 제공하지만, 사용자가 설정하지 않는 이상, 프로그램은 어떤 에디터를 호출해야할지 판단하기 어렵게 만듬. 이러한 경우를 처리하기 위한게 alternatives system

선택지를 단일 패스에 대해서 생성하는게 아니라, 그룹(master, slave) 단위로 생성하는게 특징.

## 사용법

### --install

그룹을 추가함. 존재하는 그룹이라면 add, 존재하지 않는 그룹이라면 생성한다.

```bash
sudo update-alternatives --install <link> <name> <path> <priority>
```

gcc를 예로 들면,

- link = /usr/bin/gcc
- name = gcc
- path = /usr/bin/gcc-4.8
- priority = 10 (적당한 숫자)


### --slave

같은 단위로 전환할 링크 목록을 제공. `--install`과 함께 넘김

```bash
sudo update-alternatives \
  --install <link> <name> <path> <priority> \
  --slave <link> <name> <path>
```

ex:

```bash
sudo update-alternatives \
  --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 10 \
  --slave /usr/bin/gcc g++ /usr/bin/g++-4.8
```

### --set

명령을 입력했을때 어떤 그룹을 실행할지 결정.

sudo update-alternatives --set <name> <path>

```bash
sudo update-alternatives --set gcc /usr/bin/gcc-4.8
```

### --config

`--set`의 interactive mode

```bash
sudo update-alternatives --config <name>
```

```bash
sudo update-alternatives --config gcc
```

### --remove

alternative와 그 slave를 삭제. 사용 중이라면 다른 alternative로 전환하고 삭제된다.

### --display

```bash
sudo update-alternatives --display <name>
```

`<name>`에 연동된 그룹 목록 + 현재 활성화된 그룹을 확인할 수 있음.

## Mode

기본은 우선도를 통한 자동 설정. `--set`이나 `--config`를 사용해서 변경하게 되면 자동으로 manual mode로 전환하게 됨.

자동 모드로 복구하려면 `--auto <name>` 으로 변경해 줄것.

## Reference

- https://linux.die.net/man/8/update-alternatives
- http://graziegrazie.hatenablog.com/entry/2015/11/14/101050
