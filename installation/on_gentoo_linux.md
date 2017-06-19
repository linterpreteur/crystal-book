# Gentoo Linux에서

Gentoo Linux의 주 [저장소](https://wiki.gentoo.org/wiki/Ebuild_repository/ko)는 크리스탈 컴파일러를 포함하고 있습니다.

## 설정

설정 플래그를 먼저 살펴봅시다.

```
# equery u dev-lang/crystal
[ Legend : U - final flag setting for installation]
[        : I - package is installed with flag     ]
[ Colors : set, unset                             ]
 * Found these USE flags for dev-lang/crystal-0.18.7:
 U I
 - - doc      : API, Javadoc과 같은 문서를 설치. 전역으로 설치하는 대신 패키지마다 설정하는 것이 권장됨
 - - examples : 소스 코드를 비롯한 예시를 설치
 + + xml      : dev-libs/libxml2 라이브러리로 크리스탈 XML 모듈을 사용
 + - yaml     : dev-libs/libyaml 라이브러리로 크리스탈 YAML 모듈을 사용
```

## 설치

```
su -
emerge -a dev-lang/crystal
```