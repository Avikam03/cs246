# CS 246


## _C++_

#### Range-based for loops
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

#### Encapsulate the solution 
??? i don't know what this means :(
incomplete...

#### Member operators
In OOP, the data contained within an object are called attributes or member fields. The procedures of an object are called methods, operations, or member functions.

#### Arrays of objects
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

#### Constant objects
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


#### Static members
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


#### Invariants and encapsulation: 

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
- **Protected** class members are visible to the class and its subclasses. this breaks encapsulation as child classes can break invariants! A compromise could be to make fields private and accessor
- 
- 
- 
- 
- s and mutators protected.


Note that, by default, all members of structs are publicly accessible. On the other hand, all members of a class are by default private!

incomplete... (protected definition)










## _SE: UML and design patterns_

#### Introduction to design patterns
A design pattern is a codified solution to a common software problem. It specifically deals with a problem in object oriented software development, and thus focuses on the involved classes and their relationships. A design pattern has 4 key elements:
1. a memorable name that describes the problem or solution, 
2. a description of the problem to solve, 
3. the general form of the solution, and 
4. the consequences (results and trade-offs) of using the pattern.

The guiding principle behind all ofthe design patterns is: encapsulate change on design decisions i.e. program to the interface, not the implementation. The class relationships thus rely heavily upon abstract base classes. 

Patterns are more abstract than code libraries, but more concrete than design principles (e.g. coupling, cohesion, etc.). They extend the designers' vocabulary so that there is a common way to describe problems and solutions. Design patterns help designers find a suitable, more predictable design more efficiently. They can help code be refactored and evolve more easily.

#### UML class model diagrams
A class in UML: (box with three sections)
- Class
- (Optional) Fields
- (Optional) Methods

![](https://i.imgur.com/rKobx4F.png)

Note:
- - -> private
- + -> public

Constructors and the Big 5 are not shown.

#### Iterator design pattern
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


#### purpose behind Iterator design pattern
talked about in the above section!

#### implementation and useful code
incomplete...




## To do:
- Iterator Exercise
- Previous year final exams
