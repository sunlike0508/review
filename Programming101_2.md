# 2회차

1차원 배열의 합 구하기

```java
public static int arrayAccumulator(int[] array, int sum, int index) {
    
    if(index < array.length) {
        return arrayAccumulator(array, sum + array[index], index + 1);
    }

    return sum;
}
```

```java
public static void main(String[] args) {

    int[] array = new int[]{1,2,3,4,5};
    int sum = 0;
    int index = 0 ;

    for(int i = index; i < array.length; i++) {
        sum = sum + array[i];
    }

    System.out.println(sum);
}
```

이렇게 할 경우 문제가 있다.

arrayAccumulator를 사용하는 클라이언트 입장에서 `arrayAccumulator(new int[]{1,2,3}, 20 , -15)` 이렇게 보내면 어떻게 할 것인가?

이를 위해 주절주절 주석을 달것인가?

```java
/**
 절대 index를 음수로 보내지말고 sum도 0으로 보내야 함.
 sum은 합계로 0으로 무조건 보내야
*/ 
public static int arrayAccumulator(int[] array, int sum, int index) {
    
    if(index < array.length) {
        return arrayAccumulator(array, sum + array[index], index + 1);
    }

    return sum;
}
```

## 오류와 실패

오류는 일어날 수 있는데 프로그램이 실패하지 않는 경우는 뭘까?

그것은 내부에 잇는 함수나 기능들이 내결함성을 가지는 경우가 그렇다.

* 내결함성은 시스템이 하드웨어 또는 소프트웨어의 오류, 장애 또는 고장에도 계속해서 정상적으로 동작하는 능력을 의미

```java
public static int arrayAccumulator(int[] array, int sum, int index) {
    
    if(index < 0) {
        return sum;
    }
    
    if(index < array.length) {
        return arrayAccumulator(array, sum + array[index], index + 1);
    }

    return sum;
}
```

대표적으로 위와 같은 index < 0 이면 sum을 반환하는 코드.

제대로된 index가 오지 않는 오류가 발생하면 죽어야 하나 내결함성으로 인해 sum을 리턴.

그러나 이런 코드는 클라이언트 입장에서는 결과값이 뭔지 모른다.

런타임 오류는 발생하지 않지만 `컨텍스트 오류`가 발생한다. 즉, 예상치 못한 결과 값으로 인해 내가 알 수 없는 오류가 발생.

오류 중에서 가장 무서운 오류는 컨텍스트 오류이다. 런타임 오류는 차라리 죽기 때문에 내가 빠르게 발견이 가능하다.

그러나 컨텍스트 오류는 내가 발견하기 힘들기 때문에 내결함성을 갖추는 것은 신중히 도입해야한다.

일반적으로는 무조건 좋지 않다. 따라서 내결함성을 갖추면 안된다.

```java
public static int arrayAccumulator(int[] array, int sum, int index) {

    if(index < 0) {
        throw new IllegalArgumentException("index less than 0");
    }

    if(index < array.length) {
        return arrayAccumulator(array, sum + array[index], index + 1);
    }

    return sum;
}
```

이렇게 내결함성 갖추지 않고 무조건 오류를 표출하게끔 해야한다.

결국 내결함성은 함수안에서 가 아닌 사용하는 쪽 즉, 클라이언트에게 전가했다.

```java
public static int arrayAccumulator(int[] array, int sum, int index) {
    
    if(index == 0 && sum != 0) {
        throw new IllegalArgumentException("sum should be zero when index is zero");
    }

    if(index < 0 || array.length < index) {
        throw new IllegalArgumentException("index less than 0");
    }

    return index < array.length ? arrayAccumulator(array, sum + array[index] * 2, index + 1) : sum;
}
```

함수는 함수 입장에서 올바른 상황에서만 계산하면 되는 것이지 그 외의 상황에서는 결과값을 결정할 사항이 아니다.

그것은 함수를 사용하는 입장에서 그 결과를 어떻게 정할지 결정해야 한다.

사실상 함수의 내부결함성을 가지는 함수는 메인 함수밖에 없다.

`프로그램은 왜 실패하는가` 추천. 절판됌... 

이것을 못하는 이유는 우리가 디버깅을 공부하지 않았기 때문에

디버깅을 처음 배울때 오류와 실패를 배움.

오류는 즉시 실패하지 않고 내결함성 모듈을 견디다가 더이상 실패하지 않을때 실패한다.

그러나 우리는 내결함성 레이어가 없다면 바로 실패할 수 있게 만들 수 있다.

보통 내결함성을 갖춰야 되는 경우는 드물기 때문에 일반적으로 오류를 빨리 내는 것이 좋다. 그래야 빨리 고친다.

이것은 제품의 신뢰성이랑 이어진다. 신뢰성은 고객이 우리 제품을 사용하게 만든다. 신뢰성이 떨어지면 고객은 사용하지 않는다.

안정성이라는 단어는 다르다. 안정성은 내부결함성을 높여서 고객이 프로그램 사용에 실패하지 않게 하는 것이다.

그러나 그 결과가 신뢰를 준다는 보장은 없다. 

오류는 감주쳐들지 말고 드러내게 짜야 한다.

이 다음 중요한 것은 코드의 역할이다.

역할이라는 말은 추상적인 말이니 쉽게 말하면 이 코드가 수정할때 같은 이유로 수정되는 것끼리 몰려 있을까?

이게 바로 응집성과 결합도에 대한 문제이다.

제일 중요한건 유지보수 하려면 수정하는 이유가 있을것이다. 이 이유를 생각해보자.

위에서 재귀함수를 생각해보면 수정하는 이유는 인자에 대한 검사 때문에 수정 정책이 바뀔것이다.

* 강의에서는 자바스크립 특성 때문에 array가 여러 타입을 받을 수 있어서 예외처리가 많다.

```java
public static int arrayAccumulator(int[] array, int sum, int index) {

    if(sumValidator(index, sum)) {
        throw new IllegalArgumentException("sum should be zero when index is zero");
    }

    if(elementValidator(index, array.length)) {
        throw new IllegalArgumentException("index less than 0 or more than array length");
    }

    return index < array.length ? arrayAccumulator(array, sum + array[index] * 2, index + 1) : sum;
}

static boolean sumValidator(int index, int sum) {
    return index == 0 && sum != 0;
}

static boolean elementValidator(int index, int arrayLength) {
    return index < 0 || arrayLength < index;
}
```

이렇게 바꾸면 차후에 인자값에 대한 추가 제한이 생기거나 바뀌면 elementValidator만 수정하면 된다.

거꾸로 단순한 배열의 합산이 아닌 2배씩 더 하는 정책으로 바뀌면

```java
public static int arrayAccumulator(int[] array, int sum, int index) {

    if(sumValidator(index, sum)) {
        throw new IllegalArgumentException("sum should be zero when index is zero");
    }

    if(elementValidator(index, array.length)) {
        throw new IllegalArgumentException("index less than 0 or more than array length");
    }

    return index < array.length ? arrayAccumulator(array, sum + array[index] * 2, index + 1) : sum;
}
```

이렇게 `array[index] * 2`만 바꾸면 된다.

우리가 코드를 분리하거나 함수를 분기하는 이유는 그 알고리즘이 복잡하거나 길어서가 아니다.

유지보수를 위해서 수정하는 이유가 다르다면 다른 애들을 분리시켜주는 것이다.

수정하는 이유가 달라서 분리하는 것, 이것을 역할 모델이라고 한다.

역할에 따라 분리하는 것이다. 역할은 즉, 수정 원인이라고 할 수 있다.

수정 원인이 다른 애들은 분리한다.

이제 위를 잘보면 Validator 그룹이 생긴다. Validator 그룹이 생겨서 Validator를 일괄 적용할 수 있다는 얘기다.

이것을 일반화, 일반화된 인터페이스로, 추상 Validator 모양으로 만들 수 있다.

이 두가지 경우의 합집합을 정의할 수 있다.

***자바스크립트로 하다보니 자바로 내가 만들기 너무 언어적 차이가 커서 자바스크립트로 교체***

최종적으로 아래와 같이 valiator하는 역할과 덧셈 영역으로 분리 할 수 있다.

```javascript
const validator = {
    data:[
        (list, el) => Array.isArray(list),
        (list, el) => typeof el == 'number'
    ],
    validate(list, index) {
        return this.data.every(vali => vali(list, list[index]))
    }
};

const recursive = (list, index = 0, acc = 0) => {
    if(!validator.validate(list, list[index])) throw `invalid argument, list ${list}, element ${list[index]}`;
    return recursive(list, index , acc + list[index]);
}
```

## 변수의 스코프

변수의 라이프 사이클과 스코프는 다르다. 스코프를 유지하지만 라이프 사이클은 길게도 혹은 짧게도 가져갈 수 있다.

길게 가져간다면 메모리 효율성은 나빠진다. 대신 연산을 줄일 수 있다. 반대로 연산이 많아지면 메모리 효율성은 좋아진다.


```javsscript
const arraySum = (() => {
    const elementSum = (arr, i, acc) => {
        if(arr.length === i) return acc;
        return elementSum(arr, acc + arr[i], i + 1);
    };

    return arr => elementSum(arr, 0, 0);
})
```

이렇게 쓰면 내가 i와 acc에 대해서 제약조건을 스스로 안에 넣었기 때문에 클라이언트에서 신경쓸 필요가 없다.

추가로 elementSum이라는 익명함수를 통해 우리가 배우는 쪼개서 생각하는 테일 리커시브를 표현할 수 있다.

그러면 이런 스코프를 쓰면 좋은 점은? 바로 변수의 권한.

위에서는 i와 acc의 사용 권한을 arraySum에서만 0, 0으로 강제 저장하여 사용하고 있다.

즉, 실제 사용하는 클라이언트에서는 arraySum에는 list만 보낼 뿐, i와 acc가 있는지도 모른다.

따라서 i와 acc를 클라이언트가 조작 할수 없게(알수 없게) 함으로써 arraySum만 사용 가능하도록 권한을 지정할 수 있다.

우리가 코드를 설계할떄 생각해야하는 첫번째 이유다.

우리가 코드를 짜기 어려운 이유중 하나이다. 

```javascript
const arraySum = (() => {
    const elementSum = (arr, i, acc) => {
        if(arr.length === i) return acc;
        return elementSum(arr, acc + arr[i], i + 1);
    };

    return elementSum(arr, 0, 0);
    // scope는 arraySum만 알게, lifecycle은 arraysum 호출할 때 생성되어 리턴시 제거
})();

const arraySum = (() => {
    const elementSum = (arr, i, acc) => {
        if(arr.length === i) return acc;
        return elementSum(arr, acc + arr[i], i + 1);
    };
    
    const arraySum = elementSum(arr, 0, 0);
    
    return arraySum;
    // scope는 arraySum만 알게, lifecycle은 영구적
})();
```

* 아마도 const가 static이랑 같은 역할인듯. 그래서 elementSum이라는 함수가 영구적으로 메모리에 남는다는 얘기.

내가 메모리의 상태에 따라 위 아래 잘 선택.





