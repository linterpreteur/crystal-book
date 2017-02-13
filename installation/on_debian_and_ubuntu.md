# Debian과 Ubuntu에서

Debian의 파생 배포판에서는 크리스탈의 공식 저장소를 이용하실 수 있습니다.

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

## 설치
저장소가 설정되고 나면 크리스탈을 설치하실 수 있습니다.

```
sudo apt-get install crystal
```

## 업그레이드

크리스탈의 새로운 버전이 릴리즈되면 다음을 통해 시스템을 업그레이드할 수 있습니다.

```
sudo apt-get update
sudo apt-get install crystal
```
