# JDK 1.7.0_04 버전 이상의 Oracle JVM Garbage Collector
*translated, source: <http://www.fasterj.com/articles/oraclecollectors1.shtml>*

1.7.0_04(Java 7 update 4)부터 G1 GC가 공식적으로 지원된다. 본 문서는 1.7.0_04 버전 이상의 Oracle JVM에서 사용 가능한 Garbage Collector들과 그 조합에 대한 내용이다.

## Young Generation Collectors
### 1. Serial Copy
* Eden -> Survivor 또는 Survivor 영역간 객체를 복사할 때 하나의 쓰레드를 사용하는 컬렉터이다.
* '-XX:+UseSerialGC' 옵션으로 활성화한다.

### 2. Parallel Scavenge
* 객체 복사 과정에서 여러 쓰레드를 이용해 병렬 처리하는 컬렉터이다.
* Old 영역의 Serial Mark Sweep 컬렉터 및 Parallel Scavenge Mark Sweep 컬렉터와 같이 사용할 수 있도록 만들어졌다.
* '-XX:+UseParallelGC' 옵션으로 활성화한다.

### 3. Parallel Copy
* 객체 복사 과정에서 여러 쓰레드를 이용해 병렬 처리하는 컬렉터이다.
* Parallel Scavenge 컬렉터와 달리 Old 영역의 Concurrent Mark Sweep 컬렉터와 같이 사용할 수 있도록 만들어졌다.
* '-XX:+UseParNewGC' 옵션으로 활성화한다.

### 4. G1 Young
* 힙을 여러 개의 작은 영역으로 나누어 관리하는 'Garbage First' 알고리즘을 사용하는 컬렉터이며, 기존 Young 영역처럼 여전히 Eden과 Survivor 영역을 구분한다.
* '-XX:+UseG1GC' 옵션으로 활성화한다.

## Old Generation Collectors
### 1. Serial Mark Sweep
* 'Mark-Sweep' 알고리즘을 사용하고 하나의 쓰레드로 처리하는 컬렉터이다.
* '-XX:+UseSerialGC' 옵션으로 활성화한다.

### 2. Parallel Scavenge Mark Sweep
* 'Mark-Sweep' 알고리즘을 사용하고 여러 쓰레드를 이용해 병렬 처리하는 컬렉터이다. (Serial Mark Sweep 컬렉터의 병렬화 형태)
* '-XX:+UseParallelOldGC' 옵션으로 활성화한다.

### 3. Concurrent Mark Sweep
* GC 과정의 대부분을 백그라운드로 수행하여 STW(Stop The World) 시간을 최소화하는 알고리즘을 사용하는 컬렉터이다.
* '-XX:_UseConcMarkSweepGC' 옵션으로 활성화한다.

### 4. G1 Mixed
* 힙을 여러 개의 작은 영역으로 나누어 관리하는 'Garbage First' 알고리즘을 사용하는 컬렉터이다.
* '-XX:+UseG1GC' 옵션으로 활성화한다.

## Combinations of Garbage Collectors
위에 나열된 여러가지 가비지 컬렉터들은 각각 조합해서 사용할 수 있는 항목이 제한되어 있다. (예: G1 컬렉터는 다른 컬렉터와 조합할 수 없음) 또한 기존에 존재하던 Incremental GC 방식은 Java 8에서 deprecated 되었고 Java 9에서 삭제되었다. 내부적으로 Parallel Copy와 Concurrent Mark Sweep으로 변경된다.

사용 가능한 GC 방식의 조합은 다음과 같다.

| Command Options | Resulting Collector Combination |
|-----------------|---------------------------------|
| -XX:+UseSerialGC | Young: Serial Copy<br />Old: Serial Mark Sweep |
| -XX:+UseG1GC | Young: G1 Young<br />Old: G1 Mixed |
| -XX:+UseParallelGC<br />-XX:+UseParallelOldGC<br />-XX:+UseAdaptiveSizePolicy | Young: Parallel Scavenge<br />Old: Parallel Scavenge Mark Sweep<br />Adaptive Sizing 활성화 |
| -XX:+UseParallelGC<br />-XX:+UseParallelOldGC<br />-XX:-UseAdaptiveSizePolicy | Young: Parallel Scavenge<br />Old: Parallel Scavenge Mark Sweep<br />Adaptive Sizing 비활성화 |
| -XX:+UseParNewGC<br />(Java 8에서 deprecated, Java 9에서 삭제) | Young: Parallel Copy<br />Old: Serial Mark Sweep |
| -XX:+UseParNewGC<br />-XX:+UseConcMarkSweepGC | Young: Parallel Copy<br />Old: Concurrent Mark Sweep |
| -XX:-UseParNewGC<br />-XX:+UseConcMarkSweepGC<br />(Java 8에서 deprecated, Java 9에서 삭제) | Young: Serial Copy<br />Old: Concurrent Mark Sweep |
| -XX:+UseParallelGC | Young: Parallel Scavenge<br />Old: Parallel Scavenge Mark Sweep<br />('-XX:+UseParallelGC -XX:+UseParallelOldGC'와 동일) |
| -XX:+UseParallelOldGC | Young: Parallel Scavenge<br />Old: Parallel Scavenge Mark Sweep<br />('-XX:+UseParallelGC -XX:+UseParallelOldGC'와 동일) |
| -Xincgc<br />(Java 8에서 deprecated, Java 9에서 삭제) | Young: Parallel Copy<br />Old: Concurrent Mark Sweep<br />('-XX:+UseParNewGC -XX:+UseConcMarkSweepGC'와 동일) |
| -XX:+UseConcMarkSweepGC | Young: Parallel Copy<br />Old: Concurrent Mark Sweep<br />('-XX:+UseParNewGC -XX:+UseConcMarkSweepGC'와 동일) |
| no option on most Windows | Java 8까지는 환경에 따른 JVM 기본값 적용 [(JVM 기본값 참고)](http://www.techpaste.com/2012/02/default-jvm-settings-gc-jit-java-heap-sizes-xms-xmx-operating-systems/#more-3569)<br />-Client VM: '-XX:+UseSerialGC'와 동일<br />-Server VM: '-XX:+UseParallelGC'와 동일<br />Java 9에서는 '-XX:+UseG1GC'와 동일 |
| no option on most Unix | Java 8까지는 '-XX:+UseParallelGC -XX:+UseParallelOldGC -XX:+UseAdaptiveSizePolicy'와 동일<br />Java 9에서는 '-XX:+UseG1GC'와 동일 |
| -XX:+AggressiveHeap | '-XX:+UseParallelGC -XX:+UseParallelOldGC -XX:+UseAdaptiveSizePolicy'와 동일하나,<br />서버 리소스에 따라 메모리 집약적인 작업에 최적화되도록 옵션 자동 조정 |

Concurrent Mark Sweep 컬렉터에는 GC 알고리즘에 영향을 미치는 몇 가지 추가 옵션들이 있다.
* -XX:+CMSIncrementalMode / -XX:-CMSIncrementalMode
  - Incremental Concurrent GC 알고리즘 사용 / 미사용
  - Java 8에서 deprecated, Java 9에서 삭제
* -XX:+CMSConcurrentMTEnabled / -XX:-CMSConcurrentMTEnabled
  - Parallel(multiple threads) Concurrent GC 알고리즘 사용 / 미사용
* -XX:+UseCMSCompactAtFullCollection / -XX:-UseCMSCompactAtFullCollection
  - Full GC 발생 시점에 Compaction 수행 / 미수행
