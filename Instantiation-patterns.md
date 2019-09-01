# Instantiation patterns

자바스크립트는 4가지 패턴으로 객체를 선언할 수 있다. 이 4가지 패턴은 모두 다 객체를 만들고 객체에 들어갈 메소드와 프로퍼티를 설정할 수 있다. 자바스크립트에서 객체를 선언할 때 언제나 함수로 생성된다는 특징을 지니고 있다. 함수로 선언된 객체는 하나의 정형화된 모델이 되어 인스턴스를 만드는데 사용이 된다.

### Functional

Functional 패턴으로 선언했을 때 함수 안에는 빈 객체가 만들어지고 그 안에 메소드와 프로터티가 들어가게 된 후, 객체를 반환시킨다.

    var Person = function(firstName, lastName, age) {
    	var obj = {};
    
    	obj.firstName = firstName;
    	obj.lastName = lastName;
    	obj.age = age;
    
    	obj.printName = function() {
    		console.log(this.firstName + ' ' + this.lastName);
    	}
    	obj.introduce = function() {
    		console.log('Hi, my name is ' + this.firstName + ' ' + this.lastName +'. I am ' + this.age + ' years old.');
    	}
    }
    
    var person1 = Person('John', 'Doe', 20);
    person1.printName();
    person1.introduce();

Functional 패턴의 장점은 모든 함수가 객체 내에 있기 때문에 코드를 읽고 이해하기 쉽다는 장점을 지니고 있다. 그러나 functional 패턴은 새로운 인스턴스를 만들어낼 때 마다 모든 메소드를 새 인스턴스에게 할당하므로 더 많은 메모리를 차지하게 된다는 단점을 지니고 있다.

### Functional shared

Functional shared 패턴은 메소드와 프로퍼티를 각자 다른 객체에 선언함으로써 메모리를 덜 차지하여 functional 패턴의 단점을 어느 정도 극복할 수 있다는 장점을 지니고 있다. 메소드와 프로퍼티 서로 다른 객체에 선언되어 있어 서로를 잇는 extend라는 함수 이용하여 프로퍼티가 담겨있는 객체를 반환하다.

    var extend = function(to, from) {
    	for (var key in from) {
    		to[key] = from[key];
    	}
    };
    
    var someMethods = {};
    someMethods.printName = function() {
    	console.log(this.firstName + ' ' + this.lastName);
    }
    someMethods.introduce = function() {
    	console.log('Hi, my name is ' + this.firstName + ' ' + this.lastName +'. I am ' + this.age + ' years old.');
    }
    
    var Person = function(firstName, lastName, age) {
    	var someInstance = {
    		firstName: firstName;
    		lastName: lastName;
    		age: age;
    		};
    	extend(someInstance, someMethods);
    	return someInstance;
    }
    
    var person1 = Person('John', 'Doe', 20);
    person1.printName();
    person1.introduce();

위에서 언급한 바와 같이 functional 패턴보다 메모리 관리하는데 있어 더 효율적이다. 그러나 새로운 인스턴스 생성하게 될 경우, 새 인스턴스는 부모 객체를 참조하지만 그 안에 새로운 메소드를 선언하고 새 인스턴스를 만들게되면 부모 객체와 새 인스턴스는 서로 다른 메소드를 가리키게 되어 혼돈이 생길 수 있다.

### Prototypal

Prototypal 패턴은 프로토타입 체인을 이용하여 객체를 만든다. 프로토타입 체인은 어떤 특정 객체의 프로퍼티나 메소드를 접근하려는데 없다면 [[Prototype]]이 가리키는 참조를 따라 자신의 부모 객체 역할하는 프로토타입 객체의 프로퍼티나 메소드를 찾는 것이다. Functional shared 패턴과 비슷하게 메소드와 프로퍼티를 서로 다른 객체에 선언하지만 extend 함수를 사용하여 프로퍼티와 메소드를 잇지 않고 Object.create() 함수를 사용하는데 매개변수로써 메소드가 담긴 객체가 들어간다.

    var someMethods = {};
    someMethods.printName = function() {
    	console.log(this.firstName + ' ' + this.lastName);
    }
    someMethods.introduce = function() {
    	console.log('Hi, my name is ' + this.firstName + ' ' + this.lastName +'. I am ' + this.age + ' years old.');
    }
    
    var Person = function(firstName, lastName, age) {
    	var someInstance = Object.create(someMethods);
    	someInstance.firstName = firstName;
    	someInstance.lastName = lastName;
    	someInstance.age = age;
    	return someInstance;
    }
    
    var person1 = Person('John', 'Doe', 20);
    person1.printName();
    person1.introduce();

위에 코드에서 볼 수 있는 바와 같이 prototypal 패턴은 메소드는 객체의 프로토타입에 Object.create()를 통해 이어오기 때문에 메모리를 덜 차지한다는 장점을 지녔다.

### Pseudoclassical

Pseudoclassical 패턴은 객체를 선언하는데 가장 많이 쓰이는 패턴이다. Prototypal 패턴과 마찬가지로 프로토타입 체인을 활용한다. 새로운 인스턴스를 생성할 때 new라는 키워드를 사용한다. 위에 3가지 패턴과는 다르게 프로퍼티는 객체 안에 선언하지만 메소드는 객체 밖에 Prototype이라는 키워드를 사용하여 선언한다.

    var Person = function (firstName, lastName, age) {
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
    
    var person1 = new Person('John', 'Doe', 20);
    person1.printName();
    person1.introduce();

자바스크립트에 이미 내장되어 있는 기능들을 이용하기 때문에 객체를 생성하는데 있어 가장 최적화가 잘 되어있다는 장점을 지녔다.