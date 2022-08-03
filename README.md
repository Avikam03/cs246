# CS 246


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
??? i don't know what this means :(
incomplete...

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
- **Protected** class members are visible to the class and its subclasses. this breaks encapsulation as child classes can break invariants! A compromise could be to make fields private and accessor
- 
- 
- 
- 
- s and mutators protected.


Note that, by default, all members of structs are publicly accessible. On the other hand, all members of a class are by default private!

incomplete... (protected definition)










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
- - -> private
- + -> public

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
incomplete...




## _C++_

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

**Alternate Solution:**
Abstract superclass.
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
