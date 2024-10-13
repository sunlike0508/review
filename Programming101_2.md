# 2회차

1차원 배열의 합 구하기

```java
public static int arrayAccumulator(int[] array, int sum, int index) {
    if(index == array.length) {
        return sum;
    }

    return arrayAccumulator(array, sum + array[index], index + 1);
}
```
