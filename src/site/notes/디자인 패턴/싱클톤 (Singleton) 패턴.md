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

	public static class void main(String[] args) {
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

