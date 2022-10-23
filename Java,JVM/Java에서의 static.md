## Static

Java에서 Static 키워드를 사용한다는 것은 메모리에 한번 할당되어 프로그램이 종료될 때 해제되는 것을 의미합니다.  이를 정확히 이해하기 위해서는 메모리 영역에 대한 이해가 필요합니다.

![](https://mi-nya.tistory.com/assets/img/java/2020-06-24-tip-java-07-01.png)

일반적으로 우리가 만든 Class는 Static 영역에 생성되고, new 연산을 통해 생성한 객체는 Heap영역에 생성됩니다.  객체의 생성시에 할당된 Heap영역의 메모리는 Garbage Collector를 통해 수시로 관리를 받습니다. 하지만 Static 키워드를 통해 Static 영역에 할당된 메모리는 모든 객체가 공유하는 메모리라는 장점을 지니지만,  Garbage Collector의 관리 영역 밖에 존재하므로 Static을 자주 사용하면 프로그램의 종료시까지 메모리가 할당된 채로 존재하므로 자주 사용하게 되면 시스템의 퍼포먼스에 악영향을 주게 됩니다.

# Static 변수 특징

Static 변수는 클래스 변수입니다. 객체를 생성하지 않고도 Static 자원에 접근이 가능합니다.

```Java
public class MyCalculator {
    public static appName = "MyCalculator";

    public static int add(int x, int y) {
        return x + y;
    }

    public int min(int x, int y) {
        return x - y;
    }
}

MyCalculator.add(1, 2);   //  static 메소드 이므로 객체 생성 없이 사용 가능
MyCalculator.min(1, 2);   //  static 메소드가 아니므로 객체 생성후에 사용가능


MyCalculator cal = new MyCalculator();
cal.add(1, 2);   // o 가능은 하지만 권장하지 않는 방법
cal.min(1, 2);   // o
```

## Static 변수(정적 변수)

-   메모리에 고정적으로 할당되어, 프로그램이 종료될 때 해제되는 변수

Java에서 Static 변수는 메모리에 한번 할당되어 프로그램이 종료될 때 해제되는 변수로,  
메모리에 한번 할당되므로 여러 객체가 해당 메모리를 공유하게 됩니다.

```Java
public class Person {
    private String name = "Mveloper";

    public void printName() {
        System.out.println(this.name);
    }
}
```

하지만 위와 같은 클래스를 통해 100명의 Person 객체를 생성하면, "Mveloper"라는 같은 값을 갖는 메모리가 100개나 중복해서 생성되게 됩니다. 이러한 경우에 static을 사용하여 여러 객체가 하나의 메모리를 참조하도록 하면 메모리 효율이 더욱 높아질 것입니다. 또한 "Mveloper"라는 이름은 결코 변하지 않는 값이므로 final 키워드를 붙여주며, 일반적으로 Static은 상수의 값을 갖는 경우가 많으므로 public으로 선언을 하여 사용합니다.  
이러한 이유로, 일반적으로 static 변수는 public 및 final과 함께 사용되어 public static final로 활용 됩니다.

```Java
 public class Person {
     public static final String name = "Mveloper";

     public static void printName() {
         System.out.println(this.name);
     }
 }
```

위의 코드를 보면, printName이라는 함수 역시 static 키워드가 붙어서 static메소드가 되었는데,  
static 변수는 static 메소드를 통해 접근하도록 권장되기 때문입니다.

## Static 변수(정적 변수)

Static Method는 객체의 생성 없이 호출이 가능하며, 객체에서는 호출이 불가능합니다.  
일반적으로는 유틸리티 관련 함수들은 여러 번 사용되므로 static 메소드로 구현을 하는 것이 적합한데,  
static 메소드를 사용하는 대표적인 Util Class로는 java.uitl.Math가 있습니다.

```Java
 public class Test {
     private String name1 = "Mveloper";
     private static String name2 = "Mveloper";

     public static void printMax(int x, int y) {
         System.out.println(Math.max(x, y));
     }

     public static void printName(){
        // System.out.println(name1); 불가능한 호출
        System.out.println(name2);
     }
 }
```

우리는 두 수의 최대값을 구하는 경우에 Math클래스를 사용하는데, static 메소드로 선언된 max 함수를 초기화 없이 사용합니다.  
하지만 static 메소드에서는 static이 선언되지 않은 변수에 접근이 불가능한데,  
메모리 할당과 연관지어 생각해보면 당연합니다. 우리가 Test.printName() 을 사용하려고 하는데,  
name1은 new 연산을 통해 객체가 생성된 후에 메모리가 할당됩니다.  
하지만 static 메소드는 객체의 생성 없이 접근하는 함수이므로, 할당되지 않은 메모리 영역에 접근을 하므로  
문제가 발생하게 됩니다. 그러므로 static 변수에 접근하기 위한 메소드는 반드시 static 메소드가 되어야 합니다.

## Static 변수와 Static 메소드의 활용

-   Static 변수

일반적으로 상수들만 모아서 사용하며 상수의 변수명은 대문자와 _를 조합하여 이름을 만듭니다  
또한 상속을 방지하기 위해 final class로 선언을 합니다.

```Java
public final class AppConstants{

    public static final String APP_NAME = "MyApp";
    public static final String PREF_NAME = "MyPref";        

}
```

-   Static 메소드

```Java
import java.text.SimpleDateFormat;
import java.util.Date;
import android.util.Patterns;

public final class CommonUtils {

    public static String getCurrentDate() {
        Date date = new Date();
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyMMdd");
        return dateFormat.format(date);
    }

    public static boolean isEmailValid(String email) {
        return Patterns.EMAIL_ADDRESS.matcher(email).matches();
    }

}
```