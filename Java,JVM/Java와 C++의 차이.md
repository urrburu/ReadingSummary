# Similarities and Difference between Java and C++

Nowadays [Java](https://www.geeksforgeeks.org/java/) and [C++](https://www.geeksforgeeks.org/c-plus-plus/) programming languages are vastly used in competitive coding. Due to some awesome features, these two programming languages are widely used in industries as well. C++ is a widely popular language among coders for its efficiency, high speed, and dynamic memory utilization. Java is widely used in the IT industry, It is incomparable to any other programming language in terms of software development. Let us go through the various points to compare these popular coding languages: 

오늘날 자바와 씨쁠쁠은 코딩에 널리 사용되는 프로그래밍 언어이다. 이 두 프로그래밍 언어는 몇 가지 놀라운 기능 때문에 산업에서도 널리 사용된다. C++는 효율성, 고속, 동적 메모리 활용으로 코더들 사이에서 널리 사용되는 언어이다. 자바는 IT 산업에서 널리 사용되고 있다. 소프트웨어 개발 측면에서 다른 프로그래밍 언어와는 비교할 수 없다. 이러한 인기 있는 코딩 언어를 비교하기 위해 다양한 점을 살펴보자:

### **Similarities between Java and C++**
유사점

**1. Execution:** At compile-time, Java source code or **.java** file is converted into bytecode or **.class** file. At runtime, [JVM (Java Virtual Machine)](https://www.geeksforgeeks.org/jvm-works-jvm-architecture/) will load the **.class**   file and will convert it to machine code with the help of an [interpreter](https://www.geeksforgeeks.org/compiler-vs-interpreter-2/). After compilation of method calls (using the Just-In-Time (JIT) compiler), JVM will execute the optimized code. So Java is both [compiled as well as an interpreted language](https://www.geeksforgeeks.org/difference-between-compiled-and-interpreted-language/). On the other hand, C++ executes the code by using only a compiler. The C++ compiler compiles and converts the source code into the machine code. That’s why C++ is faster than Java but not platform-independent. 

**1. 실행:** 컴파일 시 Java 소스 코드 또는 .java 파일은 바이트 코드 또는 .class 파일로 변환됩니다. 런타임에 [JVM(Java Virtual Machine)]이 .class 파일을 로드하고 [인터프리터]의 도움을 받아 이를 기계 코드로 변환합니다. 메서드 호출(JIT(Just-In-Time) 컴파일러 사용)을 컴파일하면 JVM이 최적화된 코드를 실행합니다. 그래서 자바는 컴파일 된 언어이고 인터프리트된 언어이다. 반면에 C++는 컴파일러만을 사용하여 코드를 실행한다. C++ 컴파일러는 소스 코드를 컴파일하여 기계 코드로 변환한다. 그것이 C++가 자바보다 빠르지만 플랫폼에 의존하지 않는 이유이다.


Below is the illustration of how Java and C++ codes are executed: 

The execution of a Java code is as follows:  

![](https://media.geeksforgeeks.org/wp-content/uploads/20200424202741/gfg-java-code-execution.jpg)

**Execution of a C++ Code**  

![](https://media.geeksforgeeks.org/wp-content/uploads/20200424202906/gfg-c.jpg)

**2. Features:** C++ and Java both have several [Object Oriented programming](https://www.geeksforgeeks.org/object-oriented-programming-oops-concept-in-java/) features which provide many useful programming functionalities. Some features are supported by one and some by the other. Even though both languages use the concept of OOPs, neither can be termed 100% object-oriented languages. Java uses primitive data types and thus cannot be termed as 100% Object-Oriented Language. C++ uses some data types similar to primitive ones and can implement methods without using any data type. And thus, it is also deprived of the 100% Object-Oriented title.   
Below is the table which shows the features supported and not supported by both the programming languages:   
 

Features                                     C++                             Java

Abstraction                                Yes                              Yes
Encapsulation                            Yes                              Yes
Single Inheritance                     Yes                              Yes
Multiple Inheritance                  Yes                              No
Polymorphism                           Yes                              Yes
Static Binding                            Yes                              Yes
Dynamic Binding                       Yes                              Yes
Operator Overloading               Yes                              No
Header Files                               Yes                              No
Pointers                                      Yes                              No
Global Variables                        Yes                              No
Template Class                          Yes                              No
Interference and Packages       No                              Yes
API                                                No                              Yes

**Applications:** Both C++ and Java have vast areas of application. Below are the applications of both languages: 

-   **Applications of C++ Programming language:** 
    1.  Suitable for Developing large software (like passenger reservation systems).
    2.  MySQL is written in C++.
    3.  For fast execution, C++ is majorly used in Game Development.
    4.  Google Chromium browser, file system, and cluster data processing are all written in C++.
    5.  Adobe Premiere, Photoshop, and Illustrator; these popular applications are scripted in C++.
    6.  Advanced Computations and Graphics- real-time physical simulations, high-performance image processing.
    7.  C++ is also used in many advanced types of medical equipment like MRI machines, etc.
-   **Applications of Java Programming language:** 
    1.  Desktop GUI Applications development.
    2.  Android and Mobile application development.
    3.  Applications of Java are in embedded technologies like SIM cards, disk players, TV, etc.
    4.  Java EE (Enterprise Edition) provides an API and runtime environment for running large enterprise software.
    5.  Network Applications and Web services like Internet connection, Web App Development.

**Environment:** C++ is a Platform dependent while [Java is a platform-independent programming language](https://www.geeksforgeeks.org/java-platform-independent/). We have to write and run C++ code on the same platform. Java has the **WORA (Write Once and Run Everywhere)** feature by which we can write our code in one platform once and we can run the code anywhere. 

|             |            Java         |          C++          |
|-------------|-------------------------|-----------------------|
|Platform Dependency|Platform independent, Java bytecode works on any operating system.|Platform dependent, should be compiled for different platforms.|
|Portability|It can run in any OS hence it is portable.|C++ is platform-dependent. Hence it is not portable.|
|Compilation|Java is both Compiled and Interpreted Language.|C++ is a Compiled Language.|
|Memory Management|Memory Management is System Controlled.|Memory Management in C++ is Manual.|
|Virtual Keyword|It doesn’t have Virtual Keyword.|It has Virtual keywords.|
|Multiple Inheritance|It supports only single inheritance. Multiple inheritances are achieved partially using interfaces.|It supports both single and multiple Inheritance.|
|Overloading|It supports only method overloading and doesn’t allow operator overloading.|It supports both method and operator overloading.|
|Pointers|It has limited support for pointers.|It strongly supports pointers.|
|Libraries|It doesn’t support direct native library calls but only Java Native Interfaces.|It supports direct system library calls, making it suitable for system-level programming.|
|Libraries|Libraries have a wide range of classes for various high-level services.|C++ libraries have comparatively low-level functionalities.
|Documentation Comment|It supports documentation comments (e.g., /**.. */) for source code.|It doesn’t support documentation comments for source code.|

|    |Java|C++|
|---------|------------|--------------|
|Thread Support|Java provides built-in support for multithreading. |C++ doesn’t have built-in support for threads, depends on third-party threading libraries.|
|Type|Java is only an object-oriented programming language.|C++ is both a procedural and an object-oriented programming language.|
|Input-Output mechanism|Java uses the (System class): **System.in** for input and **System.out** for output.|C++ uses **cin** for input and **cout** for an output operation.|
|goto Keyword|Java doesn’t support goto Keyword|C++ supports goto keyword.|
|Structures and Unions|Java doesn’t support Structures and Unions.|C++ supports Structures and Unions.|
|Parameter Passing|Java supports only the Pass by Value technique.|C++ supports both Pass by Value and pass by reference.|
|Global Scope|It supports no global scope.|It supports both global scope and namespace scope.|
|Object Management|Automatic object management with garbage collection.|It supports manual object management using new and delete.|
|Call by Value and Call by reference|Java supports only call by value.|C++ both supports call by value and call by reference.|
|Hardware|Java is not so interactive with hardware.|C++ is nearer to hardware.|
