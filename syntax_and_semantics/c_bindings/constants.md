# 상수

`lib` 선언에서 상수를 선언할 수도 있습니다.

```crystal
@ [Link("pcre")]
lib PCRE
  INFO_CAPTURECOUNT = 2
end

PCRE::INFO_CAPTURECOUNT #=> 2
```
