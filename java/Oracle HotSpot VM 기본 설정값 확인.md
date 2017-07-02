# Oracle HotSpot VM 기본 설정값 확인
```
$ java -XX:+PrintCommandLineFlags -version
```

* 64-bit Windows 7 JDK1.8 실행 예시
```
C:\>java -XX:+PrintCommandLineFlags -version
-XX:InitialHeapSize=131979840 -XX:MaxHeapSize=2111677440 -XX:+PrintCommandLineFlags -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC
java version "1.8.0_92"
Java(TM) SE Runtime Environment (build 1.8.0_92-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.92-b14, mixed mode)
```
* 64-bit Windows Server 2012 R2 JDK1.6 실행 예시
```
C:\Program Files\Java\jdk1.6.0_21\bin>java -XX:+PrintCommandLineFlags -version -XX:InitialHeapSize=536863552 -XX:MaxHeapSize=8589816832 -XX:ParallelGCThreads=8 -XX:+PrintCommandLineFlags -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC
java version "1.6.0_21"
Java(TM) SE Runtime Environment (build 1.6.0_21-b07)
Java HotSpot(TM) 64-Bit Server VM (build 17.0-b17, mixed mode)
```
* 64-bit Windows Server 2012 R2 JDK1.7 실행 예시
```
C:\Program Files\Java\jdk1.7.0_55\bin>java -XX:+PrintCommandLineFlags -version -XX:InitialHeapSize=536863552 -XX:MaxHeapSize=8589816832 -XX:+PrintCommandLineFlags -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC
java version "1.7.0_55"
Java(TM) SE Runtime Environment (build 1.7.0_55-b13)
Java HotSpot(TM) 64-Bit Server VM (build 24.55-b03, mixed mode)
```
* 64-bit Windows Server 2012 R2 JDK1.8 실행 예시
```
C:\Program Files\Java\jdk1.8.0_131\bin>java -XX:+PrintCommandLineFlags -version -XX:InitialHeapSize=536863552 -XX:MaxHeapSize=8589816832 -XX:+PrintCommandLineFlags -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC
java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)
```
* 64-bit CentOS 7 JDK1.6 실행 예시
```
$ /usr/java/jdk1.6.0_45/bin/java -XX:+PrintCommandLineFlags -version
-XX:InitialHeapSize=524502208 -XX:MaxHeapSize=8392035328 -XX:ParallelGCThreads=8 -XX:+PrintCommandLineFlags -XX:+UseCompressedOops -XX:+UseParallelGC
java version "1.6.0_45"
Java(TM) SE Runtime Environment (build 1.6.0_45-b06)
Java HotSpot(TM) 64-Bit Server VM (build 20.45-b01, mixed mode)
```
* 64-bit CentOS 7 JDK1.8 실행 예시
```
$ java -XX:+PrintCommandLineFlags -version
-XX:InitialHeapSize=524502208 -XX:MaxHeapSize=8392035328 -XX:+PrintCommandLineFlags -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseParallelGC
java version "1.8.0_102"
Java(TM) SE Runtime Environment (build 1.8.0_102-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.102-b14, mixed mode)
``` 
