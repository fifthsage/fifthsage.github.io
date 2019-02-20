---
title: "PHP TYPING"
categories: 
  - php
  - backend
toc: true
---

## 예시

기본

```php
int $integer;
```

null 허용

```php
?int $integer = null;
```

함수

```php
public function func(int $int): int {
  return $int;
}

public function func(?int $int): ?int {
  return $int;
}
```

## 복수 타입, 객체 배열, 다차원 배열
타이핑 사용하지 않음

## visibility type typing
private, protected 변수의 경우 getter와 setter를 이용해 typing이 가능하다.

```php
class Person
{
  private $name;
  
  public function getName(): string {
    return $this->name;
  }
  
  public function setName(string $newName) {
    $this->name = $newName;
  }
}
```

## docblock
docblock에 타이핑이 되어 있을 경우 typing을 생략해도 힌트가 가능하다.

```php
class Person
{
  /**
  * @var string
  */
  public $name;
}
```
