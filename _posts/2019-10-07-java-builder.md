---
layout: post
title: 'Java의 Builder Pattern'
author: kjham.ham
date: 2019-10-07 10:00
tags: [swtech,java,effectivejava,builder]
---

정적 팩터리와 생성자에는 똑같은 제약이 있습니다.  
선택적 매개변수가 많을 때 적절하게 대응하기가 어렵다는 점입니다.  

첫 번째 대안으로, `점층적 생성자 패턴(telescoping constructior pattern)`을 주로 사용하게 됩니다.  
그러나 매개변수가 많고, 인자로 전달하기 원치 않는 변수가 있을 때 혼란을 초래합니다.  

다음과 같이 어떤 한 생성자를 선택하여 객체를 생성했다고 봅시다.  
~~~java
NutritionFacts cocaCola = new NutritionFacts(240, 8, 100, 0, 35, 27);
~~~
네 번째 인자는 `지방`을 의미하는 인자를 전달해야하는데, 굳이 전달 할 필요가 없지만 0을 전달하였습니다.  
또한, 각 매개변수가 어떤 것을 뜻하는지 한 눈에 파악하기 어렵습니다.  

두 번째 대안으로, `자바빈즈 패턴(JavaBeans pattern)`을 알아봅시다.  
매개변수가 없는 객체를 생성 후 `Setter`를 이용하여 설정하는 방식입니다.  
~~~java
NutiritionFacts cocaCola = new NutritionFacts();
cocaCola.setServingSize(240);
cocaCola.setServings(8);
cocaCola.setCalories(100);
cocaCola.setSodium(35);
cocaCola.setCarbohydrate(27);
~~~
코드가 길어기진 했지만, 더 읽기 쉬운 코드가 되었습니다.
그러나 객체 하나를 만들려면 여러 개의 메서드를 호출해야하고, 객체가 완전히 생성되기 전까지는 `일관성(consistency)`이 무너진 상태에 놓이게 됩니다.  

세 번째 대안이 바로 `빌더 패턴(Builder pattern)`입니다.  
이 패턴은 점층적 생성자 패턴의 안정성과 자바빈즈 패턴의 가독성을 겸비하고 있습니다.  
다음과 같이 빌더패턴이 적용 가능한 클래스를 구현했습니다.  
~~~java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) { calories = val; return this; }
        public Builder fat(int val) { fat = val; return this; }
        public Builder sodium(int val) { sodium = val; return this; }
        public Builder carbohydrate(int val) { carbohydrate = val; return this; }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}
~~~

다음과 같이 호출하여 객체를 생성할 수 있습니다.  
~~~java
NutritionFacts cocaCola = new NutiritionFacts.Builder(240, 8)
    .calories(100).sodium(35).carbohydrate(27).build();
~~~
이런 방식을 메서드 호출이 흐르듯 연결된다는 뜻으로 `플루언트 API(fluent API)` 혹은 `메서드 연쇄(method chaining)`라 합니다.  
`빌더 패턴은 (파이썬과 스칼라에 있는) 명명된 선택적 매개변수(named optional parameters)를 흉내 낸 것입니다.`  

`빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋습니다.`  
각 계층의 클래스에 관련 빌더를 멤버로 정의하는 것입니다.  
추상 클래스는 추상 빌더를, 구체 클래스는 구체 빌더를 갖게 합니다.  

추상 클래스`(Pizza.java)`를 다음과 같이 구성했다고 봅시다.  
~~~java
public abstract class Pizza {
    public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SUASAGE }
    final Set<Toppoing> toppings;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        public T addTopping(Topping topping) {
            toppings.add(Objects.requiredNonNull(topping));
            return self();
        }

        abstract Pizza build();

        // 하위 클래스는 이 메서드를 재정의하여 "this"를 반환하도록 해야 합니다.
        protected abstract T self();
    }

    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone();
    }
}
~~~

구체 클래스에 추상 클래스를 상속받아 다음과 같이 구성합니다. `NyPizza.java` 입니다.  
크기 매개변수를 필수로 받는 피자입니다.  
~~~java
public class NyPizza extends Pizza {
    public enum Size { SMALL, MEDIUM, LARGE }
    private final Size size;

    public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requiredNonNull(size);
        }

        @Override public NyPizza build() {
            return new NyPizza(this);
        }

        @Override protected Builder self() { return this; }
    }

    private NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }
}
~~~

다음은 `Calzone.java` 입니다.  
소스를 안에 넣을지 선택하는 매개변수를 필수로 받습니다.  
~~~java
public class Calzone extends Pizza {
    private final boolean sauceInside;

    public static class Builder extends Pizza.Builder<Builder> {
        private boolean sauceInside = false;

        public Builder sauceInside() {
            sauceInside = true;
            return this;
        }

        @Override public Calzone build() {
            return new Calzone(this);
        }

        @Override protected Builder self() { return this; }
    }

    private Calzone(Builder builder) {
        super(builder);
        sauceInside = builder.sauceInside;
    }
}
~~~

하위 클래스의 메서드가 상위 클래스의 메서드가 정의한 반환 타입이 아닌,  
그 하위 타입을 반환하는 기능을 `공변 반환 타이핑(covariant return typing)`이라 합니다.  
이 기능을 사용하면 클라이언트가 형변환에 신경 쓰지 않고도 빌더를 사용할 수 있습니다.  

다음과 같이 인스턴스를 생성할 수 있습니다.  
~~~java
NyPizza pizza = new NyPizza.Builder(SMALL).addTopping(SAUSAGE).addTopping(ONION).build();
Calzone calzone = new Calzone.Builder().addTopping(HAM).sauceInside().build();
~~~

빌더 패턴은 상당히 유연합니다.  
빌더 하나로 여러 객체를 순회하면서 만들 수 있고, 빌더에 넘기는 매개변수에 따라 다른 객체를 만들 수 있습니다.  

빌더 패턴이 장정만 있는 건 아닙니다.  
객체를 만드려면 우선 빌더를 만들어야 하고..  
빌더 생성 비용이 크지는 않지만 성능에 민감한 상황에서는 문제가 될 수 있습니다.  
또한 점층적 생성자 패턴 보다는 코드가 장황해서 매개변수가 4개 이상은 되어야 값어치를 합니다.  

> 핵심 정리  
생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는 게 더 낫다.  
매개변수 중 다수가 필수가 아니거나 같은 타입이면 특히 더 그렇다.  
빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 훨씬 안전하다.  