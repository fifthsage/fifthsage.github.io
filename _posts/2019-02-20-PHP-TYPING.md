---
title: "PHP TYPING"
categories: 
  - php
  - backend
toc: true
---

## 예시

1. 기본

```php
int $integer;
```

2. null 허용

```php
?int $integer = null;
```

3. 함수

```php
public function func(int $int): int {
  return $int;
}

public function func(?int $int): ?int {
  return $int;
}
```

## 복수 타입
타이핑 사용하지 않음

## visibility type typing
private, protected 변수의 경우 getter와 setter로만 접근이 가능하기 때문에 return type과 parmeter type에 의해 결정이 되므로 typing이 되지 않는다.

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
