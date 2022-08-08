![](https://images.unsplash.com/photo-1515896769750-31548aa180ed?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1867&q=80)

# CS 246


Pre midterm theory 🤒

## Software Engineering: Testing
### What is testing?
Software testing is an investigation conducted to verify if the software works as intended, identify existing errors, and possibly assess the quality of the software. It is an essential part of software development. Although we usually think about development as "writing code", properly testing the software is as important as writing it.

Approaches for Testing
- Human testing - manually verify if software works as intended.
- Automated (machine) testing - test suites are implemented

### Types of Software Tests

#### Unit Tests
Unit tests are conducted at the lowest level, testing only one specific module/unit of the software. The goal of unit tests is to verify if the unit of code works as intended and identify any existing errors (bugs). This can be done, for example, by testing if the functions in a module or the methods in a class work as intended and produce the expected results.

#### Integration tests
Integration tests are conducted a level above the unit tests and aim to verify if the different modules/units of the software work correctly together. Therefore, these tests should perform operations that involve multiple modules or classes and check if they work as intended.

#### Functional tests (or system tests)
Functional tests focus on the business requirements of the application. They can be conducted by executing the application with a specific set of inputs and verifying if the application produces the correct outputs. Here, we are not concerned with how the results are being produced (which should have already been tested in the unit and integration tests), only if they are correct.

#### Acceptance tests
Acceptance tests are usually part of a formal process in which the client must verify that the produced software meets all the requirements, so it can be accepted. Client acceptance is generally a condition to move to the next phase of a project, such as installing the software for real users, generating an invoice for payment, etc. 

Sometimes, acceptance tests may be detailed into phases such as alpha testing (done at the end of the development, by a subset of users, but in the controlled environment of the developer) and beta testing

#### Regression tests
Regression tests are conducted after any modification in the software, to ensure that it continues working as intended, i.e., that the modification did not introduce new errors. Ideally, regression tests should be automated, so that they can quickly be executed after each code modification. One possible way to conduct regression tests is to create automated unit, integration, and functional tests, so they can be executed again after each modification.

#### Performance tests
Performance tests are designed to verify if the run-time performance of the system will be adequate. Some systems may work well when tested by the developer in a single-user environment but may have performance issues when tested, for example, with multiple concurrent users or with large data sets.







Post midterm content 😵‍💫

## _SE: UML and design patterns_

### Introduction to design patterns
A design pattern is a codified solution to a common software problem. It specifically deals with a problem in object oriented software development, and thus focuses on the involved classes and their relationships. A design pattern has 4 key elements:
1. a memorable name that describes the problem or solution, 
2. a description of the problem to solve, 
3. the general form of the solution, and 
4. the consequences (results and trade-offs) of using the pattern.

The guiding principle behind all ofthe design patterns is: encapsulate change on design decisions i.e. program to the interface, not the implementation. The class relationships thus rely heavily upon abstract base classes. 

Patterns are more abstract than code libraries, but more concrete than design principles (e.g. coupling, cohesion, etc.). They extend the designers' vocabulary so that there is a common way to describe problems and solutions. Design patterns help designers find a suitable, more predictable design more efficiently. They can help code be refactored and evolve more easily.

### UML class model diagrams
A class in UML: (box with three sections)
- Class
- (Optional) Fields
- (Optional) Methods

![](https://i.imgur.com/rKobx4F.png)

Note:
- `-` -> private
- `+` -> public

Constructors and the Big 5 are not shown.

### Iterator design pattern
The whole point of the iterator design pattern is to create an abstraction of a pointer to the container without exposing the pointer to the client.

We will firstly need `begin()` and `end()` methods to our iteration. Moreover, the class should support the following operations:
- != 
- ++
- *

here is an example to illustrate the iterator design pattern:
```cpp
class List {
	struct Node;
	Node *theList;

	public:
		class Iterator {
			Node *curr;
			explicit Iterator(Node *curr) : curr{curr} {} // private constructor

			public:
				int &operator*() {
					return curr->data;
				}

				Iterator &operator++() {
					curr = curr->next;
					return this;
				}

				bool operator==(Iterator &other) {
					return curr == other->curr;
				}

				bool operator!=(Iterator &other) {
					return !(*this == other);
				}
				
				friend class List;
		}

		Iterator begin() {
			return Iterator(theList);
		}

		Iterator end() {
			return Iterator(nullptr);
		}

		... // other list operations, including but not limited to addToFront()
}
```

Now the client can use iterator objects to walk the list in O(n) (linear time):
```cpp
int main () {
	List lst;
	lst.addToFront(1);
	lst.addToFront(2);
	lst.addToFront(3);
	for (List::Iterator it = lst.begin(); it != lst.end(); i++) {
		cout << *it << endl; // prints 3 2 1
	}

	// we could also use automatic type detection and use the for loop
	for (auto it = lst.begin(); it != lst.end(); i++) {
		cout << *it << endl; // prints 3 2 1
	}

	// we could alternatively also do
	for (auto l : lst) {
		cout << l << endl; // interesting. implicit of l = *it;
	}
}
```


### purpose behind Iterator design pattern
talked about in the above section!

### implementation and useful code

### Template Iterator 

Let's convert the Iterator class written before into one using Templates!

```cpp
template <typename T> class Iterator {
	public:
		T &operator*() const;
		Iterator &operator++();
		bool operator==(const Iterator &other) const;
		bool operator!=(const Iterator &other) const;
}
```

Let's write a generic function that executes some other function for each element returned by an iterator:

```cpp
template <typename Iter, typename Func> 
void for_each (Iter start, Iter finish, Func f) {
	while (start != end) {
		f(*start);
		start++;
	}
}
```

As you can see, our template function for_each works for any type Iter that supports operator*, operator++, and operator!=, and any type Func that can be called as a function. So, Iter can not only be a class like the Iterator from the example above but any other type that supports these three operations, including raw pointers! For example, we can use for_each to iterate over an array using pointers:

```cpp
void f (int n) { cout << n << endl; } 
. . .
int a [] = {1, 2, 3, 4, 5};
. . .
for_each(a, a+5, f); // prints the array
```



### Observer Design Pattern
The Observer design pattern is also known as Dependents or Publish-Subscribe.
-  Generate data: Publisher, Subject
-  Consume data: Subscriber, Observer
-  Publisher → Subscriber 
-  Subject → Observer


![](https://imgur.com/U9X0ppj.png)


Implementation:

```cpp

// subject.h
#ifndef _SUBJECT_H_
#define _SUBJECT_H_

#include "vector"

class Observer;

// this class is supposed to be abstract!
class Subject {
	private:
		vector<Observer *> observers;
	public:
		Subject();
		void attach (Observer *o);
		void detach (Observer *o);
		void notifyObservers();
		virtual ~Subject() = 0;
};


// subject.cc
#include "subject.h"

Subject::Subject() {}
Subject::~Subject() {}

Subject::attach(Observer *o) {
	observers.emplace_back(o);
}

Subject::detach(Observer *o) {
	for (auto it = observers.begin(); it != observers.end(); i++) {
		if (it == o) {
			observers.erase(it);
			break;
		}
	}
}

void Subject::notifyObservers() {
	for (auto it : observers) {
		it.notify();
	}
}


```


```cpp
// object.h

class Observer {
	public:
		Observer();
		virtual void notify() = 0;
		virtual ~Observer();
}


// object.cc
#include "object.h"

Observer::Observer() {}
Observer::~Observer() {}

```

Let's consider the scenario where we have a bunch of bettors and a horse race. In this case, our bettors are the observers, and the horse race is the subject!

```cpp
// horserace.h
#ifndef __HORSERACE_H__
#define __HORSERACE_H__ 
#include <ftream>
#include <string>
#include "subject.h"

class HorseRace : public Subject {
	std::fstream in;
	std::string lastWinner;

	public:
		HorseRace(std::string source);
		~HorseRace();

		bool runRace(); // returns true if the race is successfully run

		std::string getState();
};


// horserace.cc
#include <iostream>
#include "horserace.h"
HorseRace::HorseRace(std::string source): in{source} {}
HorseRace::~HorseRace() {}

bool HorseRace::runRace() { 
	bool result {in >> lastWinner};
	if (result) std::cout << "Winner: " << lastWinner << std::endl;
	return result;
}

std::string HorseRace::getState() { return lastWinner; }

```

```cpp
#ifndef __BETTOR_H__
#define __BETTOR_H__
#include "observer.h"
#include "horserace.h"

class Bettor: public Observer {
	HorseRace *subject;
	const std::string name;
	const std::string myHorse;
	public:
		Bettor( HorseRace *hr, std::string name, std::string horse );
		void notify() override;
		~Bettor(); 
};

#endif


#include <iostream>
#include "bettor.h"

Bettor::Bettor( HorseRace *hr, std::string name, std::string horse ) : subject{hr}, 
				name{name}, myHorse{horse} {
	subject->attach( this );
}
	
Bettor::~Bettor() {
	subject->detach( this );
}

void Bettor::notify() {
	std::cout << name << (subject->getState() == myHorse ?
	" wins! Off to collect." : " loses.") << std::endl; 
}


```

finally, our implementation looks something like

```cpp
#include <iostream>
#include "bettor.h"

int main (int argc, char **argv) {
	std::string raceData = "race.txt";
	if (argc > 1) raceData = argv[1];

	HorseRace hr{raceData};

	Bettor Larry{ &hr, "Larry", "RunsLikeACow" };
	Bettor Moe{ &hr, "Moe", "Molasses" };
	Bettor Curly{ &hr, "Curly", "TurtlePower" };

	int count = 0;
	Bettor *Shemp = nullptr;

	while (hr.runRace()) {
		if (count == 2) {
			Shemp = new Bettor{&hr, "Shemp", "GreasedLightning"};
		}
		if (count == 5) delete Shemp;
		hr.notifyObservers();
		++count;
	}

	if (count  < 5) delete Shemp;

}
```



### Decorator Design Pattern
The Decorator design pattern is intended to let you add functionality or features to an object at run-time rather than to the class as a whole. These functionalities or features might also be withdrawn. Useful when extension by subclassing is impractical. We can also add features incrementally by adding new subclasses. It does mean, however, that we have end up having lots of little objects that look rather similar to each other, and differ only in how they're connected.

![](https://imgur.com/qY4l4RD.png)

Note:
- The Decorator abstract base class contains a pointer to a Component. This lets us build a linked list of decoration objects by having a pointer to a Component. Since all of the decoration classes inherit from Component, we don't need to know what is in the linked list once it's built since the operation we're invoking is virtual (likely pure virtual) in the Component class.
- Since the ConcreteComponent inherits from Component, the linked list thus terminates with a concrete component object.
- Each call to the operation in a decoration object ends up invoking the operation in the next component. In this way, we can build up values or actions by delegating the work to the objects of which the decorated object is composed.
- New functionalities are introduced by adding new subclasses, not modifying existing code, which reduces the chance of introducing errors. (This is the Open-Closed design principle in action! It's not testable material, but anybody interested in software development should read up on the concept—you may already be using it! There's a nice discussion on it at https://stackify.com/solid-design-open-closed-principle/)


Implementation example: pizza w toppings 🥳

```c++
// pizza.h

class Pizza {
	public:
		virtual float price() const = 0;
		virtual std::string description const = 0;
		virtual ~Pizza();

};

// pizza.cc
Pizza:~Pizza() {}

```

```c++
// crustandsauce.h

class CrustAndSauce : public Pizza {
	public:
		float price() const override;
		std::string description() const override;

}


// crustandsauce.cc
float CrustAndSauce::price() const {
	return 5.99;
}

std::string CrustAndSauce::description() const {
	return "Pizza";
};

```

```cpp
// decorator.h

class Decorator : public Pizza {
	protected:
		Pizza *component;
	public:
		Decorator(Pizza *component);
		virtual ~Decorator();
};

// decorator.cc

Decorator::Decorator(Pizza *component) : component{component} {}

Decorator::~Decorator() {}
```

```cpp
// topping.h

class Topping : public Decorator {
	private:
		std::string theTopping;
		const float thePrice;
	public:
		Topping(std::string theTopping, Pizza *component);
		float price() const override;
		std::string description const override;
		
};

// topping.cc

Topping::Topping(std::string theTopping, Pizza *component) :
	Decorator{component}, 
	theTopping{theTopping},
	thePrice{0.75} {}

float Topping::price() const {
	return thePrice + component->price();
}

std::string Topping::description() const {
	return theTopping + " with " + theTopping;
}

```

Other decorators are coded the same way!

```cpp
#include <iostream>
#include <string>
#include <iomanip>
#include "pizza.h"
#include "topping.h"
#include "stuffedcrust.h"
#include "dippingsauce.h"
#include "crustandsauce.h"

int main() {
	Pizza *myPizzaOrder[3];
	myPizzaOrder[0] = new Topping{"pepperoni", new Topping{"cheese", new 
						CrustAndSauce()}};
	myPizzaOrder[1] = new StuffedCrust{ new Topping{"cheese", new 
						Topping{"mushrooms", new CrustAndSauce}}};
	myPizzaOrder[2] = new DippingSauce{"garlic", new Topping{"cheese", new 
						Topping{"cheese", new Topping{"cheese", new 
						Topping{"cheese", new CrustAndSauce}}}}};
	float total = 0.0

	for (int i = 0; i < 3; ++i) {
		std::cout << myPizzaOrder[i]->description() << ": $" << std::fixed <<
		 std::showpoint << std::setprecision(2) << myPizzaOrder[i]->price() << 
		 std::endl;
		total += myPizzaOrder[i]->price();
	}

	std::cout << "Total bill: $" << total << endl;
	
	for ( int i = 0; i < 3; ++i ) delete myPizzaOrder[i];
}
```


### Factory Method Design Pattern
The Factory Method design pattern provides an interface for object creation, but lets the subclasses decide which object to create. It is also known as the **Virtual Constructor**.

![](https://imgur.com/7AuITSI.png)

Our motivating example is going to use a video game with various types of enemies (turtles or bullets), where the type of enemy created varies based upon the current game level. For example, at an easy/normal level, you might want the random creation of enemies to be 70% turtles, and 30% bullets; but, at the hard/castle level, the proportion should instead be 70% bullets and 30% turtles. Our basic inheritance hierarchy looks like:

![](https://imgur.com/De3iy55.png)

Now, we can have a pure virtual function in level that creates an enemy. That function can then be overriden in castle and normallevel and programmed such that less bullets and more turtles are created in a normallevel, and more bullets and less turtles are created in a castle! Our UML would then look something like

![](https://imgur.com/q6YXhTd.png)

our main program would be something like this:
```cpp
// main.cc
Level *level = new NormalLevel;
Enemy *enemy = level->getEnemy()
```

Regardless of what type of level the variable level stores, `level->getEnemy()` will return an enemy!  



### Template Method Design Pattern
The Template Method design pattern defines the steps of an algorithm in an operation, but lets the subclasses redefine certain steps though not the overall algorithm's structure.

![](https://imgur.com/toVbNaX.png)


Our motivating example is going to continue from our video game example. One of our Enemy subclasses was the Turtle class. Let's extend our model to have the video game contain both red and green turtles i.e. the colour of the turtle's shell is either drawn in red, or drawn in green. However, all turtles have a head, a shell, and feet.


```cpp
// turtle.h

class Turtle {
	void drawHead();
	void drawFeet();
	virtual void drawShell() = 0;
	public: void draw();
};


// turtle.cc
#include "turtle.h"

void Turtle::draw() {
	drawHead();
	drawShell();
	drawFeet();
}

void Turtle::drawHead() {
	// draws head
}

void Turtle::drawFeet() {
	// draw feet
}

```

```cpp
// redturtle.h

class RedTurtle : public Turtle {
	virtual void drawShell() override;
};

// redturtle.cc

#include "redturtle.h"
void RedTurtle::drawShell() { 
	// draw red shell
}

```

```cpp
// greenturtle.h

class GreenTurtle : public Turtle {
	virtual void drawShell() override;
};

// greenturtle.cc
#include "greenturtle.h"
void GreenTurtle::drawShell() { 
	// draw green shell
}

```

The subclasses cannot change the way that a turtle is drawn, but they can change the way that the shell is drawn.


### Non-Virtual Interface (NVI) Idiom
This is an alternate implementation of the template method design pattern! The NVI things a few things differently:
- All public methods should be non-virtual.
- All virtual methods (exceptthe destructor) should be declared either private, or at least protected.

By following NVI, we can make it so that we can change underlying implementation without changing the interface at all.



### Visitor Design Pattern
The Visitor design pattern allows the programmer to apply an operation to be performed upon the elements contained in an object structure. New operations can be added without changing the elements on which it operates.

![](https://imgur.com/5BquyYZ.png)


To explain this pattern, let us have an example where we have an enemy and a weapon, where we need to strike the enemy using the weapon to defend ourself.

![](https://imgur.com/thEXTWF.png)


What we need is a technique called double dispatch, which requires us to combine both overloading and overriding (single dispatch). We'll pick one class, Enemy, upon which we'll apply overriding. We'll then apply overloading to the Weapon class.

Our revised class model is:
![](https://imgur.com/9T6nxYv.png)

```cpp
class Enemy {
	virtual void beStructBy(Weapon &w) = 0;
}

class Turtle : public Enemy {
	void beStruckBy(Weapon &w) {
		w.strike(*this);
	}
}

class Bullet : public Enemy {
	void beStructBy(Weapon &w) {
		w.strike(*this);
	}
}

class Weapon {
	virtual void strike (Turtle &t) = 0;
	virtual void strike (Bullet &t) = 0;
}

class Stick : public Weapon {
	void strike (Turtle &t) {
		// strike turtle with stick
	}

	void strike (Bullet &t) {
		// strike bullet with stick
	}
}

class Rock : public Weapon {
	void strike (Turtle &t) {
		// strike turtle with rock
	}

	void strike (Bullet &t) {
		// strike bullet with rock
	}
}
```

ok weird question/doubt : `*this` passes derefenced class, so how does passing it into a reference work? Don't we pass pointers to a function that needs a reference so that the new var then acts as an alias?

```cpp
Enemy * e = new Bullet{ ... }; // create a bullet
Weapon * w = new Rock{ ... }; // create a rock 
e->beStruckBy( *w );
```

voila! it works.

### Pimpl idiom
In the Pimpl Idiom, the term "Pimpl"is short for "Pointer to Implementation". Since it's an idiom it is a programming technique, not a design pattern.

We've already discussed how one of the strengths of OOP is the ability to encapsulate information and keep it out of the client's hands. However, by its very nature, our header files have to show the private data fields and methods, even if they're accessible to the client. They are part of the structure, and take up space. However, this also means that any time we change implementation details, even of the private information and/or methods, the client hasto recompile all of their code that includes the header file. There's a good discussion of why this is true at https://en.cppreference.com/w/cpp/language/pimpl.

in PIMPL, we basically take all private fields of a class and store them in a separate newly constructed struct called `classNameImpl`.  We can then store a pointer to this struct with all details in our header file. Instead of accessing private fields normally, the only change that we would have to make now is that we'd have to access the fields by writing `var->fieldname` instead of simply `fieldname`.

### Bridge Design Pattern
Won't be tested :))


### Measures of design quality: cohesion and coupling, Single Responsibility Principle
The goal to writing good code is to maintain high cohesion and low coupling! If we have high coupling, then it becomes harder to reuse individual modules since changes to one require changes to others. If we have low cohesion, then our code is poorly organized, hard to understand, and hard to maintain.

![](https://imgur.com/syKh94B.png)

If we apply the goal of low coupling and high cohesion consistently in our designs, then we're effectively applying the design principle called the Single Responsibility Principle (SRP).

## _C++_

### Range-based for loops
Range-based for loops is a C++ feature that shortens the standard iteration loop. It is available for any class with the following properties:
- class has methods begin and end that produce iterators.
- the iterator supports prefix `operator++`, `operator!=`, and `operator*`.

a ranged-based for loop looks something like this:
```cpp
for (auto n : lst) {
	cout << n << endl;
}
```

In the above example, any change to n inside the for loop would've been a change to the copy of that particular value. If we wish to change values in the list, we could instead use the range-based loop as follows:

```cpp
for (auto &n : lst) {
	++n; // Mutates the actual list item
}
```

### Encapsulate the solution 
???

### Member operators
In OOP, the data contained within an object are called attributes or member fields. The procedures of an object are called methods, operations, or member functions.

### Arrays of objects
Say we have a class `className`. 
```cpp
struct className  {
	int x, y;
	className(int x, int y);
}
```

We proceed to make an array with objects of type `className` as follows:
```cpp
className lst[5]; // stack array of 5 className objects; does not compile :(
className *lst = new className[3]; // heap allocated array of 3 className objects;
									// does not compile :(
```

This will fail as `className` doesn't have a default constructor!

incomplete...

### Constant objects
A constant object is an object whose fields can not be modified.  When the const keyword is applied on a method, it means that the method does NOT modify any private members.

**Example:**
```cpp
struct Student {
	int assns, mt, final;
	float grade() {
		return assns * 0.40 + mt * 0.20 + final * 0.40;
	}
};

const Student random1 {90, 70, 90}; // const object
cout << random1.grade()
```

Additionally, we may only call methods of const objects that promise not to modify any members. As we can see above, the method `grade()` does not modify any private members of `Student random1`. However, the code in it's current state would not compile because of the rule:

```
Only const methods may be called on const objects.
```

Thus, we can only call the function `grade()` on a constant object if the const keyword is added to it.

```cpp
float grade() const {
	return assns * 0.40 + mt * 0.20 + final * 0.40;
}
```


### Static members
These are fields that are associated with the class, instead of being associated with a particular instance of the class. These are particularly useful when we need to maintain state or data across all instances of a particular class.

**Example:**
```cpp
// student.h
struct Student {
	...
	static int numInstances;
	Student(int assns, int mt, int final): assns{assns}, mt{mt}, final{final} {
		 ++numInstances;
	}
};
```

```cpp
// student.cc
int Student::numInstances = 0;
```

As shown above, static fields must be defined external to the class. At this point, memory gets allocated for a single copy of the `Student::numInstances` field. This memory will be automatically freed when the program is terminated. Unlike normal members, since static members are not part of object instantiation, it should not be destroyed as part of object destruction!

**Static member functions** don't depend on a specific instance for their computation. They are only allowed to access static members.

```cpp
// In student.h:
struct Student {
	int assns, mt, final; static int numInstances;
	...
	// The static method howMany() can access the static field numInstances.
	// However, it cannot access the instance fields assns, mt, final.
	static void howMany() {
		cout << numInstances << endl;
	}
}; 
```

```cpp
// In main.cc:
Student billy {60, 70, 80}, jane {70, 80, 90};
Student::howMany(); // 2
```

Note how the method `howMany()` is called on `Student` and not a particular class!


### Invariants and encapsulation: 

To understand invariants, let us look at an example

```cpp
struct Node {
	int data;
	Node *next;
	Node (int data, Node *next): data{data}, next{next} {}
	. . .
	~Node() { delete next; }
 };
 
int main() {
	Node n1 {1, new Node{2, nullptr}};
	Node n2 {3, nullptr};
	Node n3 {4, &n2};
}
```

What happens when these objects go out of scope? All three are stack-allocated, so all three have their destructors run. When n1's destructor runs, it reclaims the rest of the list. But when n3's destructor runs, it attempts to delete n2, which is on the stack, not the heap! This is undefined behaviour. Quite possibly, the program will crash.

So the class Node relies on an assumption for its proper operation: that next is either nullptr or is a valid pointer to the heap.

This assumption is an example of an **invariant**—a statement that must hold true—upon which Node relies. But with the way that Node is currently implemented, we can't guarantee this invariant will hold. We can't trust the user to use Node properly.

So, an invariant is *a statement that must hold true otherwise our program will not function correctly*. It is hard to reason about programs if you can't rely on invariants.


To enforce, invariants, **encapsulation** is used. While doing this, we make clients treat our objects as black boxes (capsules) that create abstractions in which implementation details are sealed away, and such that clients can only manipulate them via provided methods. Encapsulation regains our control over our objects.

We implement encapsulation by setting the access modifier (or visibility) for each one of the members (fields or methods) of a class:
- **Private** class members can only be accessed from within the object (i.e., using the implicit this pointer of the object's methods). Therefore, any other objects (of a different type) or functions cannot directly access the private members of an object. This means that private fields cannot be read or modified from outside of the object's methods and that private methods cannot be called from outside of the object's methods. 
- **Public** class members can be accessed from anywhere. Thus, public fields can be read and modified from outside the object and public methods can be called from outside of the object.
- **Protected** class members are visible to the class and its subclasses. this breaks encapsulation as child classes can break invariants! A compromise could be to make fields private and accessors and mutators protected.


Note that, by default, all members of structs are publicly accessible. On the other hand, all members of a class are by default private!


### UML class models & inheritance
There are four main types of class relationships: association, aggregation, composition, and generalization.

**Aggregation**
Aggregation is a form of association that describes a "whole-part" relationship where one class, the aggregate or whole, is made up of the constituent part class. The end of the association with the aggregate is marked with a special symbol, an open/hollow/white diamond. Only one end of the association may be marked as an aggregate. It is possible to decorate either end of the association with a navigation arrow, or multiplicities, or constraints.

This is often represented as a "**has-a**" relationship. If A has a B:
- B exists independetly outside of A
- if A is destroyed, B lives on
- if A is copied, then B is NOT deep copied, it is shared.

Example:
A student may belong to one or more clubs, and a club may need 4 members to be a recognized one. In this case, we would model the classes and represent them as such

![](https://imgur.com/tB5F8Yx.png)


**Composition**
Composition is a stricter form of aggregation. In particular, once a "part"is joined to a "whole", it may not be shared with any other object. The whole is also responsible for destroying all of its component parts when it is destroyed. It may also be responsible for creating its components. Whether the components exist independently beforehand or are created by the owner is something to be specified in the design.

This is often described as an "Owns-A" relationship, where if A owns a B,typically:
- B has no identity or independent existence outside of A
- if A is destroyed, B is destroyed.
- If A is copied, then B is copied i.e. perform a deep copy


**Generalization/specialization**
The generalization (or specialization) association between the parent/superclass and the child/subclass is indicated by putting a triangular arrowhead on the association end that joins the parent. By definition, there is no multiplicity or navigation arrowhead, though constraints may be added. There may be separate lines from the parent class to each child, or the lines may be drawn as a tree structure.

![](https://imgur.com/nky5HY0.png)

This is often described as a "**Is-A Relationship**", where A is a B. It is implemented through inheritance

### C++ inheritance: intro to basic terminology and ideas
Lets examine this idea of inheritance more closely by looking at an example to motivate our code structure. We'd like to track our book collection. We have developed a collection of classes for this purpose. 

Each Book, Text, and Comic class have fields to hold the author, title, and length (number of pages). The Text has an additional field, the topic, while the Comic has an additional hero field

Here's the UML class model of our desired outcome:

![](https://imgur.com/a72gtRZ.png)


To solve this problem, we're going to rely upon the C++ notion of inheritance and recognize that a Text is a Book, of sorts, as is the Comic. In other words, they are books with other, additional features. Alternatively, we could describe a Text as a *specialization* of a Book, or a Book as a *generalization* of a Comic.

Book then becomes our **base** class/**parent** class/**superclass**. Text (and Comic) then becomes our **derived** class/**child** class/**subclass**.

**Why inheritance?**
Inheritance lets us use the information and methods defined in the class from which we inherit, which lets us *remove duplicate code and data*. This is a useful feature, since it reduces the number of places we have to change if we change implementations. 

Note that it is also possible to override the parent's method to replace it with the subclass's own version, which is often desirable

Implementation:
```cpp
class Book {
	std::string author, title;
	int length;
	public: 
		Book( const std::string &author, const std::string &title, int length );
		std::string getTitle() const;
		std::string getAuthor() const;
		int getLength() const;
		bool isHeavy() const;
};

class Text : public Book {
	std::string topic;
	public: 
		Text( const std::string &author, const std::string &title, int length,
			  const std::string &topic );
		std::string getTopic() const;
};


class Comic : public Book {
	std::string hero;
	public:
		Comic( const std::string &author, const std::string &title, int length,
			   const std::string &hero );
		std::string getHero() const;
};
```

The keywords "public Book" denote that the classes Text and Comic inherit publicly from Book.

Note that we do not repeat the data fields that we inherit from the base class! Doing so hides the parent's information, and is almost always an error.

Instead, our derived classes inherit the title, author and length data fields. They also take advantage of inheriting the parent class methods getTitle, getAuthor, and getLength to avoid re-writing those methods. The corresponding UML class diagram now looks like this:

![](https://imgur.com/IBdTH5u)
![](https://imgur.com/IBdTH5u)
![](https://imgur.com/IBdTH5u.png)

Note that we don't bother representing inherited fields in the UML diagram.

**Question**: Who can see the fields of Book?
**Answer**: Only an object of type Book can see them.

**Imp Question:** Since title, author, and length are part of the information that the derived classes Text and Comic inherit, can they see the fields of Book?
**Answer**: Only an object of type Book can see them. Other objects of any type derived from Book will not be able to see them!

The above question is very important to consider if you think of building classes with multiple levels of inheritance!


Now, how do we initialize these classes?
In our constructor of derived or inherited classes, we must first call the constructor of the superclass to build that portion of the object. Then, we can initialize private fields of that particular class.

Example:
```cpp
Text::Text(const std::string &title, const std::string &author, int length, const std::string &topic) : Book{title, author, length}, topic{topic} {}
```



**Alternate Approach:**
We might even consider not making fields of the superclass accessible to derived classes. We can do so by declaring members in the superclass under the protected keyword.

```cpp
class Book {
	std::string title, author;
	int length;

	protected:
		int getLength() const;

	public:
		Book(const std::string &title, const std::string &author, int length);
		std::string getTitle() const;
		std::string getAuthor() const;
		bool isHeavy() const;
		
}
```

We did this because our end goal in this case is to write isHeavy(), so that it can find out if a Book/Text/Comic is heavy or not without needing to know what type of object it is.

let us define "heavy" as:
- book with 200+ pages
- text with 400+ pages
- comic with 30+ pages


```cpp
bool Book::isHeavy() const { return length > 200; }
bool Text::isHeavy() { return getLength() > 400; }
bool Comic::isHeavy() const { return getLength() > 30; }
```

This works perfectly when dealing with `Book b, Text t, Comic c` .


### Object slicing
However, what if we did
```cpp
Comic c{"Robin Rescues Batman Twice", "Robin", 40, "Robin"};

Book b = c;

cout << "The comic book: " << c.getTitle() << "; " << c.getAuthor() << "; " << (c.isHeavy() ? "heavy" : "not heavy") << endl;

cout << "The book: " << b.getTitle() << "; " << b.getAuthor() << "; " << (b.isHeavy() ? "heavy" : "not heavy") << endl;
```

Our expected output is "heavy" in both cases. However, when compiled and run it produces the output:
```
The comic book: Robin Rescues Batman Twice; Robin; heavy
The book: Robin Rescues Batman Twice; Robin; not heavy
```

What went wrong? When we initialized b by coping object c, **object slicing** occured :(
A simple way to understand object slicing is through this diagram.

![](https://imgur.com/lRpVd2e.png)

When we copy (whether by assignment or constructor, though it's a copy constructor call in this example), we're effectively trying to squeeze a larger object into a smaller amount of memory. We lose the information that makes c a Comic, and b only sees the Book portion. So, we can't successfully store objects of our subclass types into variables of the superclass type.


How do we solve this issue?
What if there was a way for the program to dynamically examine the actual type of the pointed to object?

### Getting the right method called i.e. virtual, references/pointers, and override

We can do exactly so by using the keyword `virtual`. By declaring a virtual method in the superclass, and methods with the exact same signatures in base classes, we can achieve the required result!

We would edit the classes and their implementation would look something like this

```cpp
class Book {
	std::string author, title;
	int length;
	protected: 
		int getLength() const;
	public:
		Book( const std::string &author, const std::string &title, int length );
		std::string getTitle() const;
		std::string getAuthor() const;
		virtual bool isHeavy() const; };
}

class Text : public Book {
	std::string topic;
	public: 
		Text( const std::string &author, const std::string &title, int length,
			  const std::string &topic );
		bool isHeavy() const override;
		std::string getTopic() const;
};


class Comic : public Book {
	std::string hero;
	public:
		Comic( const std::string &author, const std::string &title, int length,
			   const std::string &hero );
		bool isHeavy() const override;
		std::string getHero() const;
};
```

where
```cpp
bool Book::isHeavy() const { return length > 200; }
bool Text::isHeavy() const { return getLength() > 400; }
bool Comic::isHeavy() const { return getLength() > 30; }
```

Now when we compile and run using the same pointer example from the previous version:
```cpp
Comic *pc = new Comic{"Spiderman Unabridged", "Peter Parker", 100, "Spiderman"};
Book *pb = pc; 
cout << "The comic book ptr: " << pc->getTitle() << "; " << pc->getAuthor() << "; " << (pc->isHeavy() ? "heavy" : "not heavy") << endl;
cout << "The book ptr: " << pb->getTitle() << "; " << pb->getAuthor() << "; " << (pb->isHeavy() ? "heavy" : "not heavy") << endl;
```

we end up with the correct output for both versions:
```
The comic book ptr: Spiderman Unabridged; Peter Parker; heavy
The book ptr: Spiderman Unabridged; Peter Parker; heavy
```


### Polymorphism
Now, from the above code, we've learned that using pointers (or references) lets us hold objects of either the parent or child types without slicing them. 

Let us now use this concept to build a container of books that can be either instances of Book, Comic, or Text. The ability to accommodate multiple types under one abstraction is called **polymorphism**. It's one of the chief benefits to inheritance!

To demonstrate polymorphism, let us add another virtual method `favorite()`, which returns true if a certain criteria is met

```cpp
// My favourite books are short books.
bool Book::favourite() const { return length < 100; }
// My favourite comics are Superman comics.
bool Comic::favourite() const { return hero == "Superman"; }
// My favourite textbooks are C++ books 
bool Text::favourite() const { return topic == "C++"; }
```


Now, our main routine creates an array of (Book*), initialized to various books, texts, and comics. It then calls the printMyFavourites function, that iterates over the array and calls favourite on each object. If favourite returns true, the title of the item is printed. Then all of the dynamically allocated memory is freed.

```cpp
// Polymorphism in action.
void printMyFavourites(Book *myBooks[], int numBooks) {
	for (int i=0; i < numBooks; ++i) {
		if (myBooks[i]->favourite()) cout << myBooks[i]->getTitle() << endl;
	}
}

int main() {
	Book* collection[] {
		new Book{"War and Peace", "Tolstoy", 5000},
		new Book{"Peter Rabbit", "Potter", 50},
		new Text{"Programming for Beginners", "??", 200, "BASIC"},
		new Text{"Programming for Big Kids", "??", 200, "C++"},
		new Comic{"Aquaman Swims Again", "??", 20, "Aquaman"},
		new Comic{"Clark Kent Loses His Glasses", "??", 20, "Superman"}
	};
	printMyFavourites(collection, 6);
	for (int i=0; i < 6; ++i) delete collection[i];
}

```






### Array of polymorphic objects
If you want to use polymorphism in combination with arrays (or any other container type), it turns out that this **will only work correctly if your array holds pointers** (an array of references requires that all references be initialized upon the array's declaration, which isn't feasible for large arrays).

check course notes for example, but in general would not recommend doing this unless pointers of objects are stored in the array just like how it was done in previous section.
### Virtual Destructor
Using polymorphism in combination with dynamic memory poses a problem!

Let us take an example to better understand the issue at hand.

```cpp
class X {
	int *x;
	public:
		X(int n) : x{new int[n]} {}
		~X() { delete [] x; }
}

class Y {
	int *y;
	public:
		Y(int n, int m): X{n}, y{new int [m]} {}
		~Y() { delete [] y; }
}

int main() {
	X x{5};
	Y y{5, 10};

	X *xp = new Y{5, 10};

	delete xp;
}
```

This will undoubtedly lead to a memory leak as the destructor of X is called, and the memory allocated in Y is left unfreed :(

Our solution would be to make X's destructor virtual!

**Key takeaway**: If a class might have subclasses, declare the destructor virtual, even if the destructor body is empty. If a class should not have subclasses, declare it final.
### Abstract base classes and pure virtual methods

To demonstrate abstract classes and pure virtual methods, let us consider three classes - Student, Coop and Regular. A student has to be one of Coop and Regular. In this case, our UML looks something like this.

![](https://imgur.com/6z3vg1x.png)

Our implementation looks something like

```cpp
class Student {
	protected:
		int numCourses;
	public:
		virtual int fees() const; // what should this do?
}

class Regular : class Student {
	public:
		int fees const override; // computes fees of regular student
}

class Coop : class Student {
	public:
		int fees const override; // computes fees of coop student
}
```

However, what goes in the implementation of student's fees? In this case, since a student HAS to be one of co-op and regular, there will be no object of type *Student*. Therefore, we can make Student be an abstract class. An abstract class cannot be instantiated and has at least one method that is not implemented. Its purpose is to organize subclasses. 

In C++, we create abstract classes by leaving methods without implementation. So, we can explicitly give Student::fees no implementation. This is done in C++ by adding = 0 to the end of the declaration of a virtual method:

To summarise, what are abstract classes? A class is abstract if: 
1. it declares a pure virtual method 
2. it inherits a pure virtual method that it does not override

Our updated implementation looks something like this:
```cpp
class Student { 
	. . . 
	public:
		 virtual int fees() const = 0;
	};
}
```

**IMP:** Subclasses of an abstract class are also abstract unless they implement all pure virtual methods.

Non-abstract classes are called concrete:
```cpp
class Regular: public Student { // concrete class
	public:
	int fees() const override { return 700 * numCourses; }
};
```

Note: In UML, represent virtual and pure virtual methods using italics. Represent abstract classes by italicizing the class name.
### Inheritance and copy/move operations
Similar to the problems we encountered earlier with the destructor and using the right method, we will encounter the same problem here if we don't declare copy and move operations in the superclass as virtual.

Thus, our implementation would look like this:
```cpp
class Book {
	...
	public:
		virtual Book &operator=(const Book &other);
		virtual Book &operator=(const Book &&other);
}

class Text {
	...
	public:
	Text &operator=(const Book &other) override;
	Text &operator=(const Book &&other) override;
}
```

**Alternate Solution:** Abstract superclass.

![](https://imgur.com/dP1ghZZ.png)

implementation would look like this:
```cpp
class AbstractBook {
	string title, author;
	int length; 
	
	protected:
	AbstractBook &operator=(const AbstractBook &other); // Assignment now protected
	AbstractBook &operator=(AbstractBook &&other); // Assignment now protected 
	
	public: 
	AbstractBook( . . . );
	virtual ~AbstractBook() = 0; // Need at least one pure virtual method 
								// If you don't have one, use the dtor
 };
```

```cpp
// In abstractbook.cc: 
AbstractBook::~AbstractBook() {}
```

```cpp
class NormalBook: public AbstractBook {
	public:
		NormalBook( . . . );
		~NormalBook();
		NormalBook &operator=(const NormalBook &other) {
			AbstractBook::operator=(other);
			return *this;
		}
		NormalBook &operator=(NormalBook &&other) {
			AbstractBook::operator=(std::move(other));
			return *this;
		}
}
```


incomplete... this is hard to understand :(
### Templates: what are they? STL
Template programming allows us to create parameterized classes (**templates**) that are specialized to actual code when we need to use them. The advantage is that we can use the template code to generate many concrete classes without having to duplicate code.

For example, let's say we need to implement a class List for int data and another for float data. We could copy and paste the code and just change the type of the private field within the Nodes. But, there's a better way to solve this problem

```cpp
template <typename T> class List {
	struct Node {
		T data;
		Node *next; . . . 
	};
	Node *theList;
	public:
		. . . };
```

Now, our List class can store any type of data. To create a new List object, we need to specify the value of the parameter T, i.e., the type of data we want to store. When the program is executed, each instance of T in the code of the List will be replaced with the actual type. For example:

```cpp
List<int> li; // int is the value of the template parameter T. // So, each T in the List's code will be replaced with int.
li.addToFront(1); 

List<string> ls; // string is the value of the template parameter T. // So, each T in the List's code will be replaced with string.
ls.addToFront("hello");
```



The Standard Template Library (**STL**) is a large collection of useful templates that already exist in C++.

It contains collection classes such as lists, vectors, maps, deques, etc., which we can use to do common tasks, as well as iterators to traverse the elements in those collections and generic functions to operate on them, such as initialization, sorting, searching, and transformation of the elements.

**std::vector**
The class `std::vector` is a generic (template) implementation of dynamic-length arrays. 

For example, you can use it to create a dynamic-length array of integers (note that it's defined in the library and in the std namespace):

```cpp
#include
using namespace std;
. . .
vector<int> v; // because it's a template, we need to specify the type of data to store, which is int in this example
v.emplace_back(6); // {6}
v.emplace_back(7); // {6, 7}
```

The methods `emplace_back` or `push_back` can be used to add elements to the vector. The <ins>difference</ins> is that emplace_back creates a *new object* by using the class constructor before adding it to the array, whereas push_back *copies or moves* the content from an existing object into the array.

`pop_back` removes the last element of the vector

looping over vectors:
```cpp
for (std::size_t i = 0; i < v.size(); ++i) {
	cout << v[i] << endl;
}

for (auto n : v) {
	cout << n << endl;
}

// to iterate in reverse
for (vector<int>::reverse_iterator it = v.rbegin(); it != v.rend(); ++it) {
	...
}

// shorter
for (auto it = v.rbegin(); it != v.rend(); ++it) {
	...
}

```


`erase` erases the item passed to it.

### Exceptions
Firstly, what can be thrown?  One can throw exception of type **int,float,long or custom data types like classes and structs**.

In C++ (and many other object-oriented languages), when an error condition arises, the function raises (or throws) an exception (note: the words throw and raise are generally used interchangeably when referring to exceptions). Then what happens? By default, execution stops. But we can write handlers to catch exceptions and deal with them.

Let's take an example where an exception is thrown if grade > 100 is entered:

```cpp
class InvalidGrade {
	...
}

int checkGrade(int grade) {
	if (grade >= 0 && grade <= 100) {
	
	} else {
		throw InvalidGrade{};
	}
}
```

Now, if we compile and run the main program, the exception will be raised when the first invalid grade is encountered. Because we did not implement an exception handler, the program execution will be stopped when the exception is raised:

```
$ ./main
terminate called after throwing an instance of 'InvalidGrade'
Aborted (core dumped)
```

**How do we handle exceptions?**
Let us try using a `try-catch` block.

```cpp
int main () {
	try {
		Student s {7899, -10, 50, 150};
		cout << "s.grade() = " << s.grade() << endl;
	} catch {
		cout << "Invalid grade :(" << endl;
	}
}
```

All the statements that go within the `try { }` block are "protected", meaning that if an exception is raised while executing any of them, the execution will move to the catch block(s). In a catch block, we can specify the type of exception that we want to handle. In this case, we are only handling exceptions that are objects of the InvalidGrade class. If an exception of this type is raised, the statements within the `catch { }` block are executed. After that, the program's execution continues on the next line after the `catch { }` block. But if an exception of any other type is raised, that catch block won't be executed and the program will terminate immediately just as if we did not have any try-catch. Thus, if you don't have a catch block that matchesthe type of the raised exception, it'sjust like not having any catch block at all.

Running and compiling right now would give us an output like this
```
./main
Invalid grade.
```

let us try passing information in the exception, to make it more client friendly!

```cpp
class InvalidGrade {
	private:
		int grade;
	public:
		InvalidGrade(int grade) : grade{grade} {}
		int getGrade() const {return grade; }
}
```

```cpp
...
} else {
	throw InvalidGrade{grade};
}
...
```

Now, let's update our exception handler by catching the thrown object into the variable ex. Then, we can read the information in the object to include it into our error message:

```cpp
// in main .cc
int main () {
	try {
		Student s {7899, -10, 50, 150};
		cout << "s.grade() = " << s.grade() << endl;
	} catch (InvalidGrade ex) {
		cout << "Invalid grade :" << ex.getGrade() << endl;
	}
}
```

And when we compile and run the program, we will see the updated message: 

```
$ ./main
Invalid grade: -10
```


What happens when an exception is raised in a call chain? What happens is that the exception keeps getting throwed till it is caught or handled, or till control is passed back to main, in which case if there is no handler, the program terminates. 

Partial Exception Handling refers to the process where a handler executes some job, where it makes corrections, and throws another exception. it could also rethrow the same exception! This is useful when a function needs to do some cleanup, but it won't be able to completely handle the error. For example, if a function allocated dynamic memory, a partial exception handler can free it before rethrowing the original exception. Therefore, the function avoids a memory leak but lets someone else handle the exception

**Exceptions in Destructors**
WARNING! NEVER let a destructor throw an exception! By default,the program will terminate immediately (`std::terminate` will be called). Although it is possible to create a throwing destructor (you would tag it with `noexcept(false)`), you still should never do this. If the destructor is being executed during stack unwinding while dealing with another exception, you now have two active, unhandled exceptions and the program will abort immediately (again, by calling `std::terminate`).

**Catching Exceptions With Subclasses And By Reference**
Firstly, let us note that we can have as many `catch {}` with one try.  

```cpp
try {
	... // here the dots are figurative - mean that there's other code here
} catch (CustomException e) {
	// handle custom error
} catch (out_of_range r) {
	// handle out of range error
} catch (...) { // literally dots here!
	// handles any other exception type
}
```

So, if an exception of type CustomException is raised in the try block, the first catch block will run. If an exception of type out_of_range is raised, the second block will run. And note that the block `catch (...) {}` acts as a catchall that handles any other type of exception not handled by the previous blocks.

If your exception classes use **inheritance**, the class hierarchy is considered by the exception handling blocks. For example, if E2 is a subclass of E1, then a catch (E1) {} will handle exceptions of classes E2 and E1. This is because, due to inheritance, an E2 is a type of E1.

```cpp
class E1 {};
class E2: public E1 {};
try { // do something
} catch (E1) {
	// will handle exceptions of type E1 or E2 
}
```

You can also write multiple catch blocks to create different handlers for specialized exceptions and base exceptions. If you do this, note thatthe exception handlers are checked in order. So,the handler for the subclass needs to appear before the handler ofthe superclass. Otherwise,the handler ofthe superclass will handle the exception first.

**Note**: When you use a catch block to catch an exception based on the superclass, such as the catch (E1) block in the example above, the object is sliced into the superclass type. This means that, if you have polymorphic methods in the classes, the methods that will run will be those of the superclass. 

To avoid this, we can simply **catch by reference** instead!

Example:
```cpp
class E1 {
	public:
		virtual void f() {
			cout << "E1" << endl;
		}
};

class E2: public E1 {
	public:
		void f() override {
			cout << "E2" << endl;
		}
}

int main() {
	try {
		throw E2{};
	} catch (E1 & e) {
		e.f();
	}
}
```


**Rethrowing Exceptions in a Class Hierarchy**
An exception can be rethrown by using the statement throw;, or by using throw s;, where s is a variable where the caught exception was stored. In general, both approaches are similar. However, they will differ if the exception is a subclass and the exception handler caught it as an object of the superclass. 

For example, if we take the above example where the exception was sliced, if we do `throw s`, the sliced exception would be thrown. Instead, if we do `throw`, the original exception (unsliced) would be thrown!

### std::map
The class `std::map` can be used to implement dictionaries, in which unique keys are mapped to values. It is a generic (template) class, so we can define any type for the keys and the values. (Well, almost any type. By default, std::map uses operator< to compare and sort the keys. So, the key must be of a type that supports operator<, unless you define a different comparison function as the optional third template parameter. Check the documentation of the std::map class for more details.)

For example, a map of string keys to int values (note that map is in the library and the std namespace):
```cpp
#include <map>
using namespace std;

...
map<string, int> m; // string is the key type, and int is the value type
m["abc"] = 1;
m["def"] = 4;
// Reading the values associated with each key
cout << m["abc"] << endl; // 1
cout << m["ghi"] << endl; // 0 (see note below)
```

If a key is not found when trying to read it, such as in the lastline above (highlighted in red), it is inserted and the value is default-constructed (for an int,the default value is zero).

`erase` can be used to delete a key and its value
`count` returns one if a key is found in the map or zero otherwise

iterating over a map:
```cpp
for (auto &p : m) {
	cout << p.first << p.second << endl; // Note: first and second are fields not 
										 // methods
}
```

p's type here is `std::pair<string, int>&` (pairs are defined in `<utility>`).

### compilation dependencies
pretty easy concept. If a class is being used, include the header file containing the declaration of that class. If only a pointer of it is being used, or if it is the return type or parameter type in a function declaration (not definition), then we only need to forward declare the class.

**Examples:**
```cpp
class B : public A {
	// must include "A.h" since compiler needs to know exactly how large the 
	// class A is to build class B
}

class C {
	A myA; 
	// we need "A.h" as compiler needs to know exactly how large class A is in 
	// order to determine the size of class C.
}

class D {
	A *myAptr;
	// All pointers are the same size, so a forward declaration in the header file 
	// for class D is sufficient, though the implementation file of D will need to 
	// include a.h.
};

class E {
	A f(A x);
	// Despite the fact that the method E::f passes a parameter of type A by value, 
	// and returns an instance of A by value, the method signature is only used for 
	// type checking by the compiler. There is thus no true compilation dependency, 
	// and a forward declaration is sufficient, though the implementation file of E 
	// will need to include a.h.
}

class F {
	void f() {
		A x;
		...
		x.someMethod();
		...
	}
};
// Because class F wrote the implementation of method F::f inline, it is using a 
// method that belongs to class A. Therefore, it must include the header file for A 
// so that the compiler knows what methods A has available; however, if we moved 
// the implementation of F::f to the implementation file of F, then we could use a // forward declaration here instead. This is another reason why it is discouraged // to write methods inline.

```
### exception safety
Consider the following function

```c++
void f() {
	MyClass mc;
	MyClass *p = new MyClass;
	g();
	delete p;
}
```

When everything runs without exceptions, no memory is leaked. p is deleted on the last line of the function, and mc is stackallocated, so the destructor will be automatically called during stack unwinding after the end of f's execution.

However, what happens if g() throws an exception? mc will still be deleted during stack unwinding. However, the last line of the function will not execute, so p will never be deleted and that memory will be leaked.

A simple solution to avoid the memory leak above is to add an exception handler to f(), which will delete p and rethrow the exception to continue the stack unwinding:

```cpp
void f() {
	MyClass mc;
	MyClass *p = new MyClass;
	try {
		g();
	} catch (...) {
		delete p; // this works, but duplicates the line of code that we already 
	}			//had below throw;
	delete p;
}
```

This solution works, i.e., the memory leak is solved. However, it's ugly and error-prone. If we forget the exception handler like the one we added to f, we will only detect the memory leak if we test a situation when g throws an exception.

It would be better if we could guarantee that something (here, delete p;) will happen, no matter how we exit f (normally or by exception). It turns out that, as we saw in the previous topic, that's exactly what RAII does—it guarantees that resources will be freed at the end of the function. So, let's see how we can take advantage of that to provide exception safety to our functions.

Generally speaking, there are three levels of exception safety for a function f:
1. **Basic guarantee**: if an exception occurs, the program will be in some valid, unspecified state. Nothing leaked, class invariants maintained. 
2. **Strong guarantee**: if f throws or propagates an exception, the state of the program will be as if f had not been called.
3. **No-throw guarantee**: f will never throw an exception and will always accomplish its task.

#### Basic Guarantee:
As we saw above, we can use a simple exception handler to ensure that memory leaks don't occur. However, as RAII has taught us, it would be even better to not allocate memory on the stack at all! For example, here, we could simply make p a unique_ptr!

```c++
void f() {
	MyClass mc;
	auto p = std::make_unique<MyClass>();
	g();
}
```

Here, p is a stack allocated smart pointer. Thus, p's destructor will be called automatically as a part of stack unwinding regardless of how f ends. p's destructor will then of course free the dynamic memory allocated by the smart pointer without failure.

Thus, using RAII is a good way of ensuring that memory leaks don't occur and basic guarantee is maintained.

#### Strong Guarantee:
As stated above, a strong exception guarantee means that if a function f throws or propagates an exception, the state of the program will be as if f had not been called. This means that any modification in the program state made by f needs to be undone if an exception is thrown. Let's see an example:

```cpp
class A { . . . };

class B { . . . };

class C { 
	A a;
	B b;
	public:
		void f() {
			a.g(); // may throw (provides strong guarantee)
			b.h(); // may throw 
		}
};
```

is C exception safe? if `a.g()` throws, nothing happens as state hasn't changed yet.
However, if `b.h()` throws, and the effects of `a.g()` are non local, then state of class C has changed! The changes made would have to be undone for class C to offer strong guarantee. Otherwise, it just offers basic guarantee :(

So, how do we solve this problem? We could try making copies of the private members!

```cpp
class C {
	A a;
	B b;
	public:
		void f() {
			A atemp = a;
			B btemp = b;
			atemp.g(); // If this throws, original a and b still intact
			btemp.h(); // If this throws, original a and b still intact
			a = atemp; // But what if copy assignment throws?
			b = btemp;
		
		}
}
```

This solution is almost perfect. It doesn't consider what happens if the copy assignment or copy constructor methods throw!

So, what change do we make to make sure this can't happen? A good solution would be to use the PIMPL idiom!

```cpp

struct CImpl {
	A a;
	B b;
}

class C {
	unique_ptr<CImpl> pImpl;

	public:
		void f() {
			auto temp = make_unique<CImpl>(*pImpl);
			temp->a.g();
			temp->b.h();
			std::swap(pImpl, temp); // no possibility of throwing since we are 
									// simply swapping pointers!
		}
}
```

Note that the pImpl idiom is not the only way to accomplish this, it's only one ofthe possible ways to do it.

In fact, pImpl may not be the best solution if your data includes a collection (vector, map, etc.) because making a temporary copy of everything would mean copying the whole collection, which could be inefficientif the collection contains a large number of elements and only a few of them need to be modified. If that's the case, it would be better to just make copies of the objects you need to modify instead of using pImpl and copying everything.

Generally, a function or method can only offer a strong guarantee if allthe functions or methods thatit calls offer a strong or a no-throw guarantee.

When a function or method offers a strong guarantee, you should always documentit.

#### No-Throw Guarantee
Every function in C++ is either non-throwing or potentially throwing.

Non-throwing functions guarantee that they will never throw or propagate an exception. Therefore, if an exception is thrown by a non-throwing function, the program is automatically terminated.

In general, the default compiler-provided versions of the: constructor, copy constructor, move constructor, copy assignment operator, move assignment operator, and destructor are non-throwing, although there are some exceptions to this rule (which you can read about here if interested).

Any other function will be potentially throwing unless you declare it with noexcept:
```cpp
void f() noexcept; // the function f() does not throw
```

You can also pass an expression to noexcept. If it evaluates to true, then the function is declared as non-throwing:
```cpp
void f() noexcept(true); // the function f() does not throw; same as just noexcept 
void f() noexcept(false); // the function f() is potentially throwing; same as if 
							//you did not use noexcept at all
```

When you're writing a function that you know that can never throw an exception, it is a good idea to declare it with noexcept. As explained in the section above, using non-throwing functions allow other functions to also offer the no-throw or the strong guarantee

Pay special attention to the move constructor and move assignment operator. If allthey do is swap basic values or pointers,they will never throw an exception. Ifthat's the case, always declare them with noexcept. Doing so allows collection classes such as std::vector to be more efficient when storing objects of that class,


### Resource Acquisition Is Initialization (RAII)
The RAII idiom states that you should only EVER acquire resources as a result of initializing a stack-allocated object whose job is to manage that resource.

### smart pointers: std::unique_ptr, std::shared_ptr

A unique pointer is a class whose job is to be an object to manage pointers, so you don’t need to manage pointers on your own.

Example: int pointers in a vector
```cpp
vector<unique_ptr<int>> f(int s, int (*g)(int)) {
	vector<unique_ptr<int>> v;
	for (int i = 0; i < s; i++) {
		v.emplace_back(make_unique<int>(i));
		*v[i] = g(*v[i])
	}
}
```

make_unique allocates memory. it could fail, g could fail, or our initial initialization of our vector could fail.

- if vector fails, similar to when allocating the array failed, nothing happens as nothing has happened in our function.
- if make unique fails, if we put 5 things in our vector, and the 6th call fails. An exception is thrown, this functions stack frame is popped off the stack. Since v is a vector, its destructor is called. v contains unique pointers, which are also classes, which will also call its destructor.

class `std::unique_ptr<T>` is a class that holds a `T*`, which you supply in the vector (`unique_ptr<int> p {new int{10}}`) OR `make_unique<T>` returns a `unique_ptr<T>` and takes as args the ctor params or initializing data for T.

`unique_ptr` and `make_unique` are in `<memory>`. the dtor of `unique_ptr` frees the pointer, in between you can deref the unique_ptr just like a ptr.

unique_ptrs are unique….
```cpp
unique_ptr<c> p{new c{...}};
unique_ptr<c> q = p; // ERROR!
```

However, you can still use raw pointers for non-owning pointers, but you must be careful
```cpp
void foo (int *p) {
	*p = *p + 5; 
}
```

```cpp
int main() {
	unique_ptr<int> p {new int {10}};
	foo(p.get());
} 
// unique.ptr<i>::get returns the underlying raw pointer.
```

Beware:
```cpp
int *p = nullptr;
if (...) {
	unique_ptr<int> q {new int{10}};
	p = q.get();
}
*p = *p + 5; // dangling
```

Here, we’re not following RAII. Dangling pointer, and hence memory error occurs.

If however, there is true shared ownership, i.e, any of several pointers may need to free the data, you can use `std::shared-ptr`.

```cpp
{ 
	// shared-ptr<myclass>
	auto p1 = std::make_shared<myclass>();
	// allocates space for my past objects, p1 points at it.
	if (....) {
		auto p2 = p1; // copy construction
	} // p2 is popped, it doesn't free the memory it points at
} // p1 is popped off the stack and it does free the memory.
```

shared_ptrs maintain a reference count, a count of all, shared_ptrs pointing at the same object, the memory is freed when the ref count reaches 0.

However, beware that something can go wrong in cases like:
```cpp
{
	int *p = new int{5};
	shared_ptr<int> sp1{p};
	if (...) {
		shared_ptr<int> sp2{p};
	} // p has been freed now
} // double free since p is already freed
```




### exception safety & stl vector
**STL Vectors**
The STL vectors encapsulate a heap-allocated array and follow RAII: when a stack-allocated vector goes out of scope, the internal heap-allocated array is freed. For example:

```cpp
void f() {
	vector <myClass> v;
	. . . 
} // v goes out of scope; array is freed, MyClass destructor runs on all objs in 
	// the vector
```

However, in the case where
```cpp
void g() {
	vector <myClass *> v;
	. . .
} // v goes out of scope; pointers don't have destructors; only the array is freed
```

Pointers don't have destructors. So, in the case of a vector of pointers, any objects pointed to by the pointers in v are not freed. The vector v has no way of knowing whether deleting those pointers may be appropriate. The pointers might not own the objects they're pointing at; the objects might not even be on the heap. So if these objects need to be freed, you have to do it manually:

```cpp
for (auto &x : v) delete x;
```

Alternatively, we could use smart pointers!

```cpp
void h() {
	vector<unique_ptr<myClass>> v;
	. . .
} // array is freed; unique_ptr destructors run, so the objects ARE deleted
```

unique_ptrs have destructors, which are run by the vector's destructor when v goes out of scope. Then, the memory pointed at by the unique pointers are deleted and there are no memory leaks.

Therefore, using vectors of smart pointers instead of vectors of regular pointers is a good way to write exception-safe functions

Let’s consider how `vector<r>::emplace_back` or `push_back` works.

- offer the strong guarantee
- If the array is full, (i.e size == cap)
	- allocates new larger array
	- adds the new element to this array
	- copies the objects over from the old (copy ctor).
		- if the copy ctor throws
			- destroy new larger array
			- old array still intact.
			- strong guarantee
	- delete old array.

BUT copying is expensive. If the old array is just going to be thrown away. Furthermore, how can we create a vector of unique_ptr if they can’t be copied??

Wouldn’t moving the objects from the old array to the new be more efficient?
- Allocate new larger array
- Place new object in it
- Move the objects over (move ctor)
- delete the old array

But, `emplace_back` offers the strong guarantee. if one of those moves fail, how does the function offer the strong guarantee?

The problem if we move: if the move ctor throws then `vector<T>::emplace_back` can no longer offer the strong guarantee because old array is no longer intact. But, emplace-back promises the strong guarantee.

Therefore, if the move ctor offers the no throw guarantee, then emplace_back will use the move ctor, otherwise it will use the copy ctor, which may be slower.

So, why is it important for emplace_back to place the new object at the front?
incomplete...

So, your move operations should provide the no throw guarantee if possible, and you should indicate that they do!

```cpp
class myClass {
	public:
		myClass(myClass &&other) noexcept { ... }
		myClass &operator = (myClass &&other) noexcept { ... }
};
```

If you know a function will never throw or propogate an exception, declare it noexcept. It facilitates optimization. At a minimum, your class’ swap and move operations should be non-throwing.

If you follow PIMPL, it is trivial for your classes to have nothrow swap and move operations as they only deal with pointers.


### casting: static_cast, reinterpret_cast, const_cast, dynamic_cast

in C:

```cpp
Node n;
int *ip = (int *) &n;
// trust me bro this is an int
```

A cast forces c++ to treat a `Node *` as an `int *`.
Casts should be avoided - If you **MUST** cast, you should use C++ style casts.

C++ style casts come in 4 varieties:

1. **static_cast**: is for “sensible casts”, with well defined behaviour.

Example: a double to int

```cpp
double d {...};
void f(int x);
void f(double d); // let's say, for whatever reason, we want to call the int version on d.
f(static_cast<int> d); // calls the int version
f(d); // calls the double version
```

The other common usecase is when we have a superclass pointer to a subclass pointer. BUT, we must know it actually points at that object.

```cpp
Book *b = new Text{...};
Text *t = static_cast<Text *>(b); // here, we are taking responsibility that b 
// actually points to a text. this is safe in this case
```

If it doesn’t really point to a text, it is undefined behaviour! Don’t lie to the compiler. If you do, it will get its revenge ‼️

2. **reinterpret_cast**: is for unsafe, implementation dependent, “weird conversions”.

Out of all casts, this should be avoided the most. Almost all uses of reinterpret_cast result in undefined behaviour.

```cpp
Student s;
Turtle *t = reinterpret_cast<Turtle *> (&s); // forces a student to be treated like 
											// a turtle
t->beStruckBy(Stick{y});
```

3. **const_cast**: is for converting between const and non-const. it is the only c++ style cast that can “cast away constness”. Most usage of this will be bad.

    Main usage: you are using a library that someone else wrote, and they have a function that doesn’t declare its parameter const but you know that it doesn’t really change its parameters. If you know for a fact that it doesn’t really change the parameters, and you want to use the function with a const value of yours, you can use const_cast.

```cpp
void g(int *p); // you know g doesn't actually modify *p
void f(const int *p) {
	... 
	g(const_cast<int *>(p));
}
```

On the other hand, if g does change `*p`, this is very bad !!!

4. **dynamic_cast**: is it safe to convert a `Book*` to a `Text *`.

```cpp
Book *pb = ...;
Text *t = dynamic_cast<Text *>(pb); // this is a safe cast only if pb is actually 
									//pointing to a text
```

`dynamic_cast<T *>(p)` returns p (as a `T *`) if p actually points at a T. Else, it returns a nullptr.

Works by looking at the virtual table pointer of that object.

Only works on hierarchies with at least one virtual function.

**Why is it bad design?**
```cpp
void whatIsIt(Book *pb) {
	if (dynamic_cast<Text *> (pb)) {
		cout << "Text" << endl;
	} else if (dynamic_cast<Comic *> (pb)) {
		cout << "Comic" <, endl;
	} else {
		cout << "book" << endl;
	}
}
```

- It’s highly coupled with the book hierarchy. Any changes to the book hierarchy would need changes here.
- It is completely against polymorphism.

It makes sense to simply have a virtual function that returns “Comic” or “Text”.
However, in entirety, dynamic casting in most cases defeats the purpose of polymorphism.


But, all of these operations are on raw pointers. Can we do equivalent operations on smart pointers? Yes
```cpp
// defined in <memory>
static_pointer_cast
const_pointer_cast
dynamic_pointer_cast
```

Dynamic casting also works on references.
```cpp
Text t {...};
Book &b = t;
Text &t2 = dynamic_cast<Text &> (b);
```

If b actually refers to a Text, then the `dynamic_cast` returns a `Text &` to it, if not, since there is no such thing as a null reference, it raises the exception` bad_cast`.

With `dynamic_cast`, we can (if we want) implement a polymorphic assignment operator.

We can now write

```cpp
Text &operator=(const Book &b) { // virtual in base class
	const Text &t = dynamic_cast<Text &> (b);
	if (this == &t) return *this;
	Book::operator=(t);
	topic=t.topic; // we couldn't do this before‼️
	return *this;
}
```


But, this hasn’t really solved the problem, just passed the buck onto the client who must now handle the exceptions raised by mixed assignment through base class ptrs/refs. It’s still true that polymorphic assignment doesn’t really make sense, so preferred solution is still as it was before.

All base classses should be abastract, and make the assignment operator protected in the base class.
### Revisiting the rule of 5 i.e. finally fixing copy assignment
incomplete...
### Multiple inheritance

The problem:

```cpp
struct A {
	int a;
};

struct B : public A {
	int b;
}

struct C : public A {
	int c;
}

struct D : public B, public C {
	int d;
}
```

```cpp
D d;
d.a = 15; // error
// D has two a's, one from its B component, and one from its C component.
d.B::a = 15;
d.C::a = 15; // this works ‼️
```

What do these two a fields represent? they represent the a of our superclass A. However, we have 2. It makes sense to only have one a.

the deadly diamond problem / the diamond of death / the deadly diamond of death

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/7db70543-b073-4943-a686-cd7965784b26/IMG_7334.jpg?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220804%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220804T171809Z&X-Amz-Expires=86400&X-Amz-Signature=87fda9d66864b0fd21ca824c2a3064b220c9d07947dd85de5f72b511b93b96e5&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22IMG_7334.JPG.jpg%22&x-id=GetObject)

What we really want is a singular A field that represents our A component. We can achieve this through virtual inheritance.

```cpp
struct A {
	int a;
};

struct B : public virtual A {
	int b;
};

struct C : public virtual A {
	int c;
};

struct D : public B, public C {
	int d;
};

D d;
d.a = 15; // WORKS
```
