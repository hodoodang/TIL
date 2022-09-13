---
{"dg-publish":true,"permalink":"//singleton/","dgHomeLink":true,"dgPassFrontmatter":false}
---


written by: hodoodang
created at: 2022-09-09
tags: #DesignPattern 

---

# 싱클톤 (Singleton) 패턴
**인스턴스를 오직 한 개만 제공하는 클래스**

- 시스템 런타임, 환경 세팅에 대한 정보 등, 인스턴스가 여러 개 일 때 문제가 생길 수 있는 경우가 있다. 이를 위해 인스턴스를 오직 한 개만 만들어 제공하는 클래스가 필요하다.
	- 이러한 인스턴스에는 글로벌하게 접근이 가능해야 한다.

```java
public class Settings {

	private static Settings instance;
	
	public Settings() {}

	public static Settings getInstance() {
		if (instance == null) {
			instance = new Settings();
		}
		return instance;
	}
}
```


```java
public class App {

	public static void main(String[] args) {
		Settings settings = Settings.getInstance();
		Settings settings1 = Settings.getInstance();
		System.out.println(settings == settings1);
	}
}
```

이렇게 하나의 인스턴스만을 만들어 사용해주어야 매번 호출할 때마다 같은 인스턴스를 얻을 수 있다.

그러나 이렇게 사용할 때 멀티 스레드 환경에서 안전하지 않다. 왜냐하면 인스턴스를 생성하는 순간에 대해 여러 스레드가 동시에 접근하게 되면 여러 인스턴스를 생성하게 될 수 있다.

이를 해결하기 위한 방법은 다음과 같다.

1. `sychronized` 키워드를 사용

```java
public class Settings {

	private static Settings instance;
	
	public Settings() {}

	public static synchronized Settings getInstance() {
		if (instance == null) {
			instance = new Settings();
		}
		return instance;
	}
}
```

성능 저하 우려가 있다.

2. instance를 미리 생성

```java
public class Settings {

	private static final Settings INSTANCE = new Settings;
	
	public Settings() {}

	public static Settings getInstance() {
		return INSTANCE;
	}
}
```

`INSTANCE`를 생성하는 비용이 크지 않은 경우

3. double checked locking 사용

```java
public class Settings {

	private static volatile Settings instance;
	
	public Settings() {}

	public static Settings getInstance() {
		if (instance == null) {
			synchronized (Settings.class) {
				if (instance == null) {
					instance = new Settings();
				}
			}
		}
		return instance;
	}
}
```

멀티 스레드가 동시에 `getInstance`에 접근하는 극히 드문 경우에만 `synchronized` 가 동작하기 때문에 성능 저하가 덜하다.

4. static inner 클래스 사용하기

```java
public class Settings {
	
	public Settings() {}

	public static class SettingsHolder {
		private static final Settings INSTANCE = new Settings();
	}

	public static Settings getInstance() {
		return SettingsHolder.INSTANCE;
	}
}
```

## 싱글톤 패턴 구현 방범 깨트리기

1. 리플렉션 사용

```java
public class App {

	public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, InstantiationException {
		Settings settings = Settings.getInstance();

		Constructor<Settings> constructor = Settings.class.getDeclaredConstructor();
		constructor.setAccessible(true);
		Settings settings1 = constructor.newInstance();

		System.out.println(settings == settings1);
	}
}
```

2. 직렬화 & 역직렬화 사용

```java
public class Settings implements Serializable {  
  
    private Settings() {}  
  
    private static class SettingsHolder {  
        private static final Settings INSTANCE = new Settings();  
    }  
  
    public static Settings getInstance() {  
        return SettingsHolder.INSTANCE;  
    }  
}
```

```java
public class Application {  
    public static void main(String[] args) throws IOException, ClassNotFoundException {  
  
        Settings settings = Settings.getInstance();  
        Settings settings1 = null;  
  
        try (ObjectOutput out = new ObjectOutputStream(new FileOutputStream("settings.obj"))) {  
            out.writeObject(settings);  
        }  
  
        try (ObjectInput in = new ObjectInputStream(new FileInputStream("settings.obj"))) {  
            settings1 = (Settings) in.readObject();  
        }  
          
        System.out.println(settings == settings1);  
    }  
}
```

**역직렬화 대응 방안**

```java
protected Object readResolve() {  
	return getInstance();  
}  
```

역직렬화의 경우 내부적으로 `readResolve` 함수를 호출하여 `new Settings()`를 리턴하도록 동작한다.  `Settings` 클래스에 `readResolve` 함수를 오버라이딩을 하여 언제나 `getInstance()`를 리턴하도록 한다.

이처럼 역직렬화의 경우 싱클톤 패턴을 깨뜨리는 것에 대한 방지가 가능하지만 리플렉션의 방법을 사용하는 경우에는 방지가 불가능하다.  
(물론 리플렉션은 일반적으로 사용하는 방법이 아니며, 이렇게까지 싱글톤 패턴을 깨뜨리는 경우는 싱글톤 패턴을 사용하지 않겠다는 것과 마찬가지이다.)

**리플렉션 & 역직렬화 대응 방안**

리플렉션과 역직렬화 모두에 대해 대응할 수 있는 방법으로는 `enum`으로 Settings를 사용하는 방법이 있다.

```java
public enum Settings {  
    INSTANCE;  
}
```

이를 사용하면 리플렉션으로 강제로 newInstance를 사용하는 경우 다음과 같은 Exception을 일으킨다. 이는 리플렉션에서 자체적으로 `enum` 클래스의 newInstance를 막아두었기 때문이다.

```sh
Exception in thread "main" java.lang.IllegalArgumentException: Cannot reflectively create enum objects
	at java.lang.reflect.Constructor.newInstance(Constructor.java:417)
```

`enum`의 경우 기본적으로 `Serializable`를 구현하고 있어 직렬화를 지원한다. 그리고 역직렬화를 하는 경우에도 언제나 같은 동일한 객체를 반환한다.

단, `enum`을 사용하는 경우 프로그램을 실행할 때 객체를 미리 생성해둔다는 단점이 있다.
또한 상속을 사용하지 못한다는 단점이 있다.

## 결론

결론적으로 각 상황에 따라 다음 2가지 방법을 권장한다.
- static inner 클래스 사용
- `enum` 클래스 사용

## 복습

- 자바에서 enum을 사용하지 않고 싱글톤 패턴을 구현하는 방법은?
- private 생성자와 static 메소드를 사용하는 방법의 단점은?
- enum을 사용해 싱글톤 패턴을 구현하는 방법의 장점과 단점은?
- static inner 클래스를 사용해 싱글톤 패턴을 구현하라.

# 실무

실제 자바 환경에서 내장된 기능 중 자바 어플리케이션 실행 환경에 대한 설정을 담당하는 `Runtime` 클래스가 이 싱글톤 패턴으로 구현되어 있다.

## ex)
```java
public static void main(String[] args) {
	Runtime runtime = Runtime.getRuntime();
	System.out.println(runtime.maxMemory());
	System.out.println(runtime.freeMemory());
}
```
> 싱글톤 패턴으로 프로그래밍 되어 있기 때문에 `new` 키워드로는 생성할 수 없으며, `getRuntime()` 함수를 통해서만 객체를 가져올 수 있다.

스프링 환경에서는 `ApplicationContext` 내부에서 빈으로 주입된 객체에 대해 싱글톤 스코프로 취급되며 사용한다.

## ex)
```java
/*
* SpringConfig.java
*/
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {
	public String hello() {
		return "hello";
	}
}
```

```java
/*
* SpringExample.java
*/
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class SpringExample {
	public static void main(String[] args) {
		ApplicationContext applicationContext = 
		new AnnotationConfigApplicationContext(SpringConfig.class);

		String hello = applicationContext.getBean("hello", String.class);
		String hello2 = applicationContext.getBean("hello", String.class);
		
		System.out.println(hello == hello2)
	}
}
```
> Spring의 Bean Scope인 싱글톤 스코프는 싱글톤 패턴과는 엄밀하게 따지면 다르다.

추가글: [[Java/자바와 스프링의 싱글톤|자바와 스프링의 싱글톤]]