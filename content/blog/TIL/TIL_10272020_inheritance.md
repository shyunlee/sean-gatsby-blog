---
title: '[TIL] OOP - Inheritance '
date: 2020-10-27
# 16:21:13
category: 'TIL'
draft: false
---

# How OOP Inheritance wokrs

### Prototype

- 청사진? 또는 원형 객체라고 이해할 수 있다. _어떤것(thing 또는 object?)_ 을 생성하면 그 자체가 객체의 property가 되는 prototype object 가 생성이 된다.
- 그 *어떤것*을 생성하면 *어떤것*의 data는 그것이 포함되어 있는 prototype 객체의 constructor(생성자) 라는 키값의 value로서 들어가게된다.

### **proto**

- 만약 어떤 Prototype object를 통해 그 prototype이 갖고 있는 property나 method들에 접근하여 사용할 수 있는 자식객체(상속을 받은)를 생성한다면 그 자식객체는 그 **prototype object**의 **Instance** 가 되고 그 Instance 의 **proto**라는 property를 이용해서 부모객체의 prototype이 뭔지 확인 할 수 있다.

### Inheritance

Instance 를 생성하는 방법에는 여러가지가 있지만 이미 이전 블로그에서([TIL - OOP](https://shyunlee.netlify.app/TIL/TIL_10272020-OOP/)) 다뤘으니 이번에는 Inheritance 방식에 대해서 JavaScript ES6 문법을 이용한 Pseudoclassical 방식과 Prototypal 방식을 비교하며 깊게 공부해보자.

- **Inheritance in Prototypal way**

  - `Object.create()` 라는 최상위 객체라고 할 수 있는 Object.prototype의 내장 method create을 써서 어떤 object의 property 또는 method를 상속받는 Instance 를 생성할 수 있다.

  ```
  let Human = function () {
      this.name = name
      this.catetory = 'human'
  }

  Human.prototype.sleep = function () {
      console.log(this.name + ' is sleeping now')
  }

  let john = new Human('john')

  john.name // => john
  john.category // => human
  john.sleep()
  // => john is sleeping now
  ```

  Human 이라는 Prototype 객체가 생성되었다. Human.prototype 은 constructor 와 sleep 이라는 property를 갖고있다.
  new 키워드를 사용하여 Human.prototype 객체의 instance john을 만들었다.
  dot notation 을 통해 Human.prototype의 method를 사용할 수 있다.

  ```
  let Student = function () {
  }
  ```

  Student라는 Protype 객체가 생성되었고 constructor property를 갖고 있는 Student Prototype 객체를 생성하였다.<br>
  **_나의 목적은 Student 객체의 instance를 만들었을 때 그 instance가 Human 객체의 property 또는 method를 사용할 수 있게 하는 것이다._**
  그렇게 하기 위해서는 Student객체는 Human객체를 상속받아야한다.
  Student.prototype 를 Human.prototype을 바탕으로 하는 객체로서 하게 하는 것이 `Object.create(object.prototype)`의 역할이다.

  ```
  Student.prototype = Object.create(Human.prototype)

  Student.prototype.learn = function (name) {
  console.log(this.name + ' is studying coding')
  }

  ```

  Student.prototype 은 Human.prototype을 상속받은 객체가 된다. 그 다음 learn이라는 method를 Student prototype object 에 추가한다.
  그럼 이제 Student.prototype의 instance 객체를 생성해서 동작해보자.

  ```

  let david = New Student()

  ```

  **여기서 Keypoint** `Object.create()`을 사용해서 Human을 상속받은 Student의 instance로 생성된 david는 constructor 값을 잃어버리게 된다. 이 때, 다음의 방법으로 Student의 instance가 constructor 로서 자신을 바라볼 수 있게 설정을 해줘야한다.

  ```

  Student.prototype.constructor = Student

  ```

  그럼 이제 insatance david 를 동작해본다.

  ```

      david.sleep()
      // => undefined is sleeping now
      david.learn()
      // => undefined is studying coding
      david.catogory
      // => undefined

  ```

  Human의 `sleep` method와 Student의 `learn` method둘다 접근하여 사용가능한걸 확인 할 수 있지만 Human prototype의 `this.name`, `this.category` 는 상속을 못받아서 `undefined`로 찍히는걸 볼 수 있다. Method는 상속을 받았지만 Property는 상속을 받지 못한것이다. 이것은 Human의 context가 Student에 전달이 되지 않기 때문에 Student를 this로서 Human Property를 다시 생성해는 방법으로 상속을 받는다.

  ```

  let Student = function (name) {
  Human.call(this, name)
  // this.name = name
  // this.catogory = human
  this.category = 'student' // catogory 의 값을 재할당한다.
  }

  ```

  지금까지 Prototypal 방식으로 Prototype 상속과 상속받은 Prototype을 통해 Instance를 생성하고 설정하는 것을 공부하고 연습해봤다.
  다음으로 JavaScript ES6의 문법의 `class` 키워드를 사용한 Pseudoclassical한 방법으로 inheritance와 instantaion을 연습하고 공부해보자.

  - **Inheritance in ES6 JavaScript Syntax**

  Pseudoclassical 예제를 class keyword를 사용하여 구현해본다.

  ```
  class Human {
      constructor (name) {
        this.name = name
        this.category = category
      }

      sleep () {
        console.log(this.name + ' is sleeping)
      }
  }
  ```

  Human 이라는 prototype 을 생성했다. Human 의 instance john을 생성하여 작동해본다.

  ```
  let john = new Human('john')
  john.name //=> john
  john.category //=> human
  john.sleep //=> john is sleeping
  ```

  이제 Human 을 상속받은 Student를 생성한다. 상속받을 때는 `extends` keyword 를 사용해 상속받을 객체를 지정한다.

  ```
  class Student extends Human {
      constructor (name) {
        this.name = name
        this.category = 'student'
      }

      learn () {
        console.log (this.name + ' is studying coding')
      }
  }
  ```

  이 때 `let david = new Student('david')`를 실행하면 에러가 난다. 이유는 Student의 constuctor 부분은 Human 에서 상속받는것인데 마치 새로 설정하는 것처럼 작동하기 때문이다. 그렇기 때문에 \*부모 class의 consturtor와 property가 자식 class 에서도 똑같이 사용한다면 constructor 는 설정하지 않거나 또는 constructor 안에 `super` keyword를 써서 부모 class의 property를 갖고 올 수 있다. Student class 에 grade 라는 새로운 property 를 추가한다고 할 때 아래 코딩을 보면서 두가지 방식을 확인한다.

  ```
  // First way
  class Student extends Human {
      grade = 6

      learn () {
        console.log (this.name + ' is studying coding')
      }
  }

  // Second way
  class Student extends Human {
      constructor (name) {
        super(name)
        this.grade = 6
      }

      learn () {
        console.log (this.name + ' is studying coding')
      }
  }
  ```

  **`super` keyword는 자식 class에서 생성자를 설정할 때 부모 class의 property를 받아올 때 사용한다. constructor의 parameter 부분을 동일하게 받는다.**
