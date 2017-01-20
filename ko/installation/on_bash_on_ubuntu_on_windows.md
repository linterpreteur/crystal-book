# Windows에서 Bash on Ubuntu를 이용하여

크리스탈은 __아직은__ Windows를 지원하지 않습니다. 하지만 Windows 10을 사용하신다면 [Windows의 실험적 환경인 Bash on Ubuntu](https://msdn.microsoft.com/en-us/commandline/wsl/about)를 통해 (실험적으로) 크리스탈을 이용해 볼 수 있습니다. 설치법은 [Debian 및 Ubuntu](on_debian_and_ubuntu.md)의 경우와 같지만, 몇 가지 주의해야 할 사항이 있습니다.

주의하세요. **이 방법은 아주 실험적입니다**.

## 저장소 설정

먼저 APT 설정에 저장소를 추가해야 합니다. 커맨드 라인에서 명령어를 입력하여 손쉽게 설정할 수 있습니다.

```
curl https://dist.crystal-lang.org/apt/setup.sh | sudo bash
```

서명 키와 저장소 설정이 추가될 것입니다. 직접 이 과정을 다루고 싶으시다면, *루트* 권한으로 다음 명령어를 실행하세요.

```
apt-key adv --keyserver keys.gnupg.net --recv-keys 09617FD37CC06B54
echo "deb https://dist.crystal-lang.org/apt crystal main" > /etc/apt/sources.list.d/crystal.list
apt-get update
```

## 의존성
크리스탈 프로그램을 컴파일하기 위해서는 C 컴파일러(`cc`)가 링커(`ld`)를 필요하므로 이를 설치해야 합니다.

```
sudo apt-get install clang binutils
```

## 설치
저장소가 설정되어 있으며, 의존성이 설치되고 나면 크리스탈을 설치하실 수 있습니다.

```
sudo apt-get install crystal
```

## 업그레이드

크리스탈의 새로운 버전이 릴리즈되면 다음을 통해 시스템을 업그레이드할 수 있습니다.

```
sudo apt-get update
sudo apt-get install crystal
```
