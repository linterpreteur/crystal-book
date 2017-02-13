# RedHat과 CentOS에서

RedHat의 파생 배포판에서는 공식 크리스탈 저장소를 이용하실 수 있습니다.

## 저장소 설정

먼저 YUM 설정에 저장소를 추가해야 합니다. 커맨드 라인에서 명령어를 입력하여 손쉽게 설정할 수 있습니다.

```
curl https://dist.crystal-lang.org/rpm/setup.sh | sudo bash
```

서명 키와 저장소 설정이 추가될 것입니다. 직접 이 과정을 다루고 싶으시다면, 다음 명령어를 실행하세요.

```
rpm --import https://dist.crystal-lang.org/rpm/RPM-GPG-KEY

cat > /etc/yum.repos.d/crystal.repo <<END
[crystal]
name = Crystal
baseurl = https://dist.crystal-lang.org/rpm/
END
```

## 설치
저장소가 설정되고 나면 크리스탈을 설치하실 수 있습니다.

```
sudo yum install crystal
```

## 업그레이드

크리스탈의 새로운 버전이 릴리즈되면 다음을 통해 시스템을 업그레이드할 수 있습니다.

```
sudo yum update crystal
```
