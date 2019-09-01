# ES6 Class

# ES6 Class란 무엇인가?

ES6 class란 ES6가 나오면서 functional, functional-shared, prototypal, pseudoclassical와 같이 객체 프로토타입 상속 패턴으로 소개되었다. 이는 자바스크립트처럼 프로토타입 기반 객체 지향 프로그래밍 언어에 익숙하지 않은 사용자들이 쉽게 쓸 수 있도록 만들어졌다고 한다.

    function Person(firstName, lastName, age) {
    	this.firstName = firstName;
    	this.lastName = lastName;
    	this.age = age;
    }
    
    Person.prototype.printName = function() {
    	console.log(this.firstName + ' ' + this.lastName);
    }
    
    Person.prototype.introduce = function() {
    	console.log('Hi, my name is ' + this.firstName + ' ' + this.lastName +'. I am ' + this.age + ' years old.');
    }
    
    var Student = function(firstName, lastName, age, major) {
    	Person.apply(this, arguments);
    	this.major = major;
    }
    Student.prototype = Object.create(Person.prototype);
    Student.prototype.constructor = Student;
    Student.prototype.introduce = function() {
    	Person.prototype.introduce.call(this);
    	console.log('Hi, my name is ' + this.firstName + ' ' + this.lastName +'. I am ' + this.age + ' years old. I major in ' + this.major +'.');
    }

위는 pseudoclassical 패턴을 따라 객체와 관련 메소드를 선언하였다..

ES6 에서는 그럼 어떻게 프로토타입을 선언할까?

    class Person {
    	constructor(firstName, lastName, age) {
    		this.firstName = firstName;
    		this.lastName = lastName;
    		this.age = age;
    	}
    	printName () {
    		console.log(this.firstName + ' ' + this.lastName);
    	}
    	introduce () {
    		console.log('Hi, my name is ' + this.firstName + ' ' + this.lastName +'. I am ' + this.age + ' years old');
    	}
    }

ES6에서는 class란 키워드를 사용하여 선언하고 객체 안에 property를 설정할 때 다른 패턴과는 달리 ','을 찍어줄 필요없이 메소드를 제외하고 constructor () 객체를 만들어 생성하고 초기할 수 있다. 또한,  메소드를 선언할 때 function을 붙일 필요없이 class 안에 바로 메소드 이름하고 ()을 붙여주면 된다.

    class Student extends Person {
    	constructor(firstName, lastName, age, major) { 
    		super(firstName, lastName, age);
    		this.major = major;
    	}
    	introduce () {
    		super.introduce () 
    		console.log('Hi, my name is ' + this.firstName + ' ' + this.lastName +'. I am ' + this.age + ' years old. I major in ' + this.major +'.');
      }
    }

ES6 class는 extends라는 키워드를 사용하여 새 객체에 기존 객체로부터 모든 property를 상속 받고 추가적으로 super를 사용하여 기존의  constructor와 메소드 이름을 가져와 수정을 할 수 있다. Person으로부터 상속 받은 Student는 기존에 있던 Person 클래스에 있던 constructor를 super 그대로 가져오고 거기에 더불어 major가 새로운 property로써 Student의 constructor 안에 추가됐다. 그리고 메소드도 super 사용해 가져와서 수정할 수 있다.

    var student = new Student("John", "Doe", 20, "Computer science");
    student.printName(); // 'John Doe'
    student.introduce(); // 'Hi, my name is John Doe. I am 20 years old. I major in Computer science.'

ES6 class에서 ES5의 get과 set의 비슷한 메소드는 static이다.

    // ES5
    function random () {
    	this.randomNumber = Math.random();
    }
    random.compare = function(left, right) {
    	return left.random > right.random;
    }
    
    //ES6
    class random () {
    	constructor() {
    		this.randomNumber = Math.random();
    	}
    	static compare (left, right) {
    		return left.random > right.random;
    	}
    }