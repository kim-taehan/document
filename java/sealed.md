## Sealed Class
> Sealed Class, Interface는 간단하게 상속하거나(extends), 구현(implements)할 클래스를 지정해두고  
> 해당 클래스들만 상속 혹은 구현을 허용하는 키워드입니다. (자바 17)

### final과 비교
> final 은 상속자체를 못하게 하지만 sealed 는 permits 키워드를 통해 상속이 가능한 interface, class를 지정할 수 있다.   
> 단 sealed 도 permits으로 지정받지 않는 interface, class 는 상속받을 수 없다

### sealed type의 하위 type 
> sealed type을 상속, 구현하게 되면 final, sealed, non-sealed 3가지 중에 하나를 다시 사용해야 한다.
> non-sealed 는 sealed 를 아예 해제하는 기능으로 일반 클래스처럼 사용할 수 있다.


### 도형으로 sealed class 샘플

![image](https://github.com/kim-taehan/document/assets/52950400/53d4ce3b-db0f-413b-b410-9d2a1bbf3257)


```java
sealed interface 도형 permits 원형, 다각형 { }
    final class 원형 implements 도형 { }
    sealed interface 다각형 extends 도형 permits 사각형, 삼각형 { }
        sealed class 사각형 implements 다각형 permits 정사각형 { }
            final class 정사각형 extends 사각형 { }
        non-sealed class 삼각형 implements 다각형 { }
            class 이등변삼각형 extends 삼각형 { }
            final class 정삼각형 extends 삼각형 { }
```


#### 도형 interface 
> selaed interface 로 구현은 원형 class, 상속은 다각형 interface 만 가능하다
```java
sealed interface 도형 permits 원형, 다각형 { ... }
```

#### 원형 class 
> final class 로 상속이 불가하며, selaed type의 하위에서 일반 클래스로는 지정할 수 없다
```java
final class 원형 implements 도형 { ... }
```

#### 다각형 interface 
> selaed interface 로 구현은 사각형, 삼각형 class 만 가능하다
```java
sealed interface 다각형 extends 도형 permits 사각형, 삼각형 { ... }
```

#### 사각형 class 
> sealed class 로 상속은 정사각형 class 만 가능하다
```java
sealed class 사각형 implements 다각형 permits 정사각형 { ... }
```

#### 정사각형 class 
> final class 로 더이상 상속이 불가하다
```java
final class 정사각형 extends 사각형 { ... }
```

#### 삼각형 class 
> non-sealed class 로 상속에 제한이 없어진다
```java
non-sealed class 삼각형 implements 다각형 { ... }
```

#### 이등변삼각형 class 
> 일반적인 class 로 선언할 수 있다 (상위 클래스가 non-sealed)
```java
class 이등변삼각형 extends 삼각형 { ... }
```

#### 정삼각형 class 
> final class 로 선언할 수 있다 (상위 클래스가 non-sealed)
```java
final class 정삼각형 extends 삼각형 { ... }
``` 





