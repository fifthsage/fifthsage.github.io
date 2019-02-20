---
title: "PHP TYPING"
categories: 
  - php
  - backend
toc: true
---

## 예시

1. 기본

``php
int $integer;
``

2. null 허용

``php
?int $integer = null;
``

3. 함수

``php
public function func(int $int): int {
  return $int;
}

public function func(?int $int): ?int {
  return $int;
}
``
