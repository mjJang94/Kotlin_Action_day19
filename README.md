# 시퀀스 연산 실행: 중간 연산과 최종 연산
시퀀스에 대한 연산은 중간연산과 최종연산으로 나누어진다.   
중간 연산은 다른 시퀀스를 반환하고, 그 시퀀스는 최초 시퀀스의 원소를 변환하는 방법을 알고 있다.   
최종 연산은 결과를 반환하는데 결과는 최초 컬렉션에 대해 변환을 적용한 시퀀스로부터 일련의 계산을 수행해 얻을 수 있는 컬렉션이나 원소, 숫자 또는 객체이다.
<code>
<pre>
sequence.map{...}.filter{...}.toList()
        |    중 간 연 산      |최종연산|
</code>
</pre>
중간 연산은 항상 지연 계산된다.

<code>
<pre>
listOf(1, 2, 3, 4).asSequence()
    .map{print("map($it)"); it * it}
    .filter{print("filter($sit)"); it % 2 == 0}
</code>
</pre>
이 코드를 실행하면 아무 내용도 출력되지 않는다. 이는 map과 filter변환이 늦춰져서 결과를 얻을 필요가 있을 때 적용된다는 뜻이다.(최종 연산이 호출될 때)

<code>
<pre>
listOf(1, 2, 3, 4).asSequence()
    .map{print("map($it)"); it * it}
    .filter{print("filter($sit)"); it % 2 == 0}
    .toList()
map(1) filter(1) map(2) filter(4) map(3) filter(9) map(4) filter(16)
</code>
</pre>
최종 연산을 호출하면 연기됐던 모든 계산이 수행된다.   
이 예제를 통해 연산 수행 순서를 잘 알아둬야 한다. 직접 연산을 구현한다면 map함수를 각 원소에 대해 먼저 수행해서 새 시퀀스를 얻고, 그 시퀀스에 대해 다시 filter를 수행할 것이다.   
컬렉션에 대한 map과 filter는 이런식으로 동작한다. 하지만 시퀀스에 대한 map과 filter는 좀 다르다.   
시퀀스의 경우 모든 연산은 각 원소에 대해 순차적으로 적용된다. 즉 첫 번째 원소가 처리되고, 다시 두 번째 원소가 처리되며 이런 처리가 모든 원소에 대해 적용된다.   
따라서 원소에 연산을 차례대로 적용하다 결과가 얻어지면 그 이후의 원소에 대해서는 변환이 이뤄지지 않을 수도 있다.

코틀린의 시퀀스는 자바의 스트림의 개념과 같다. 코틀린에서 같은 개념을 따로 구현해 제공하는 이유는 안드로이드 등에서 예전 버전 자바를 사용하는 경우 자바 8에 있는 스트림이 없기 때문이다.

# 시퀀스 만들기
지금까지 살펴본 시퀀스 예제는 모두 컬렉션에 대해 asSequence() 를 호출해 시퀀스를 만들었다. 시퀀스를 만드는 다른 방법으로는 generateSequence 함수를 사용할 수 있다.   
이 함수는 이전의 원소를 인자로 받아 다음 원소를 계산한다.   
아래 코드는 generateSequence로 0부터 100까지의 합을 구하는 예제이다.

<code>
<pre>
val naturalNumbers = generateSequence(0) { it+1 }
val numberTo100 = naturalNumbers.takeWhile { it <= 100 }
println(numberTo100.sum()) //------- 모든 지연 연산은 "sum"의 결과를 계산할 때 수행된다.
5050
</code>
</pre>
이 코드도 최종 연산을 수행하기 전까지는 시퀀스의 각 숫자는 계산되지 않는다.   
