# C++

[online compiler](https://gcc.godbolt.org/) to try out new features (new
standard that has not yet appeared on public distributed compilers or compile
for different platform)

# Contents

+ [General](#general)
+ [Variables and data types](#variables-and-data-types)
+ [Containers](#containers)
	+ [vector](#vector)
	+ [map](#map)
	+ [set](#set)
+ [Flows](#flows)
+ [Streams](#streams)
	+ [Stream manipulator](#manipulator)
+ [Libraries](#libraries)
	+ [iostream](#iostream)
	+ [algorithm](#algorithm)
	+ [chrono](#chrono)
+ [Functions](#functions)
+ [Exceptions](#exceptions)

# General

Hello world example:
```c++
#include <iostream>
using namespace std;

int main(void) {
	cout << "Hello, world";
	return (0);
}
```

## Compiler

Common flags:
+ `-pedantic-errors` flag to disable compiler extensions for gcc/g++
+ `-std=c++11` to indicate what standard to support - need this for uniform
initialization
+ `-Wall -Weffc++ -Wextra -Wsign-conversion` - gcc/g++ flags to increase warning
level
+ `-Werror` - gcc/g++ flag to treat warnings as errors

## Comments

Multiline comments (cannot be nested):
```c++
/* This is a multiline comment
 * with some text 1
 * with some text 2
 */
```

Proper use of comments
+ at the library, program, or function level describe how
+ inside the library, program, or function level describe how
+ at the statement level describe why
+ show why one decision was made over the other


# Variables and data types

C++ supports 3 ways to initialize a variable:
+ `int width = 5;` - copy initialization
+ `int width(5);` - direct initialization
+ `int width{5};` - uniform(brace) initialization, good for disallowing
"narrowing" conversions, for example int width{4.5} wil cause a compilation
error because of type error (copy and direct would just drop fractional part)
+ `int width{};` - zero initialization

Best practices:
+ favor uniform initialization
+ use explicit zero initialization for direct usage:
```c++
int x{0};
std::cout << x;
```
+ use zero initialization if the value is later replaced:
```c++
int x{};
std::cin >> x;
```
+ minimize variable visibility

Initializing multiple variables, valid examples:
```c++
int a = 5, b = 5;
int c(5), d(5);
int e{5}, f{5};
```

Assigning containers like strings and vector perform deep copying, that is
contents of the containers rather than pointer to container.

## Data types

Common types:
```c++
#include <string>

int number = 1;
double d = -1.5;
char symbol = 'A';
string str = "example"; // requires string header file
bool status = false;
```

String can also be compared with `==`, `!=`, `>`, `<` operators.

---

### Struct

```c++
#include <vector>
#include <string>

// can set default values for structures
struct Person {
	string name = "Name";
	int age = 0;
};

vector<Person> staff;
staff.push_back({'Jack', 22});
staff.push_back({'Io', 15});
cout << "Io's age is " << staff[1].age;
```

---

### Class

```c+++
class Route {
	public:
		// default constructor - user tells compiler it still wants to create
		// empty object (with no constructor at all, compiler was adding
		// default)
		Route() {}
		// constructor
		Route(const string& source, const string& destination) {
			//some initialization
		}
		// destructor
		~Route() {
			cout << "I'm erased" << endl;
		}
		// const method (tells compiler it won't change anything in the object
		string GetSource() const {
			return source;
		}
	private:
		string source;
};

void PrintRoute(const Route& route) {
	cout << route.GetSource << endl;
}

// anywhere where class is declared (return type, parameter type, container
// type) object if this class can be created on-the-fly by specifying its
// constructor arguments (or non for default) between curly braces
PrintRoute({});
PrintRoute({"pointA", "pointB"});
vector<Route> routes;
routes.push_back({"pointA", "pointB"});
```

When some function sets its paramater as const reference to an object, then all
methods it is going to use must be declared as `const` in the class declaration.

Object of a class is destroyed as soon as it gets out of its scope: if/else
block, while/for iteration, function call (object as parameter). In the case
when function returns an object of the class and it is not saved, it is
destroyed right after return from that function.

`explicit` keyword before constructor forces user to explicitly call object's
constuctor, instead of short syntax:
```c++
struct Day {
	int value;
	explicit Day(int new_value) {
		value = new_value;
	}
}

struct Month {
	int value;
	explicit Month(int new_value) {
		value = new_value;
	}
}

struct Year {
	int value;
	explicit Year(int new_value) {
		value = new_value;
	}
}

struct Date {
	int day;
	int month;
	int year;

	Date(Day new_day, Month new_month, Year new_year) {
		day = new_day;
		month = new_month;
		year = new_year;
	}
}

int main() {
	// works
	Date date = {Day(10), Month(11), Year(2012)};
	// doesn't work because of explicit keyword
	Date another_date = {{11}, {10}, {2011}};
	return 0;
}
```

## Overloading operator for sorting algorithms

```c++
bool operator<(const Obj& lhs, const Obj& rhs) {
	// custom logic
}
```

# Containers

## Vector

```c++
#include <vector>
#include <string>
using namespace std;

// print contents of vector by passing constant reference
void PrintVector(conts vector<string>& v) {
	for (string s : v) {
		cout << s << endl;
	}
}

int main(void) {
	vector<int> v1; // initialize empty vector
	vector<int> v2(2); // initialize vector of size 2
	vector<int> nums = {1, 2, 3}; // initialize in place
	
	// iterate over elements by reference and write to them
	vector<string> v3;
	for (string& s: v3) {
		cin >> s;
	}
	PrintVector(v3);

	// add elements
	v1.push_back(3);

	// get size of vector
	cout << v1.size() << endl;

	// access elements of vector
	v1[0] += 1;

	// initialize vector with default elements
	vector<bool> v3(10, false);

	// resize existing vector
	v3.resize(15);

	// resize existing vector and initialize its elements
	v3.assign(15, true);

	// clear vector
	v3.clear()

	// insert elements to the end from a range
	nums.insert(end(nums), begin(nums), end(nums));
}
```

## Map

```c++
#include <map>
#include <iostream>

void PrintMap(const map<string, int>& m) {
	for (auto item : m) {
		cout << item.first << ": " << item.second << endl;
	}
}

int main() {
	map<string, int> n_to_v;
	n_to_v["one"] = 1;
	n_to_v["two"] = 2;
	cout << "number is" << n_to_v["two"];
	
	// remove key-value pair by passing key to erase method
	n_to_v.erase("one");
	return 0;
}
```

Keys inside map are sorted, thus, for loop iterates over sorted list.

When user tries to access the key first time, compiler already creates a
key-value pair with the default value. For example, when one creates a
`map<string, int>` to count words, the following cycle is sufficient:
```c++
vector<string> words;
map<string, int> counter;
for (const auto& word : words) {
	++counter[word];
}
```

In standard 17, there is a new syntax for iterating over map:
```c++
map<string, int> m = {{"one" : 1}, {"two" : 2}};
for (const auto& [key, value] : m) {
	cout << key << ": " << value << endl;
}
```

## Set

```c++
#include <set>
#include <vector>

void PrintSet(const set<string>& s) {
	for (auto item : s) {
		cout << item << endl;
	}
}

int main() {
	set<string> s;

	// adding elements
	s.insert("some_text");
	s.insert("another");

	PrintSet(s);

	// remove elements
	s.erase("another");
	
	// check if elements exists
	if (s.count("this_text") == 1) {
		cout << "this_text_exists" << endl;
	}

	// create set from existing vector
	vector<string> v = {"a", "b", "a"};
	set<string> s1(begin(v), end(v));
	return 0;
}
```

The order of elements are sorted alphabetically by default. Set doesn't fail on
adding the same element, but it won't be actually added.

Set can be compared, just like vectors and maps.

# Flows

[Alternative logical operators available in standard
C++](https://en.cppreference.com/w/cpp/language/operator_alternative)

`auto` keyword can be used so that compiler can decide what is a type of inner
objects.

## while and do-while

```c++
int n = 5;
int sum = 0;

while (n > 0) {
	sum	+= n;
	n--;
}

sum = 0;

do {
	sum += n;
	n--;
} while(n > 0);
```

## for loop

Iterate over all elements of a container(must specify the type of inner
elements). Can add `const` and `&` (for passing elements by reference),
otherwise for takes a copy of an element.

```c++
string x = "abcd";
vector<int> nums = {1, 2, 3};
for (char c : x) {
	cout << c << ",";
}
for (int c : nums) {
	cout << c << ",";
}
for (auto c : nums) {
	cout << c << ",";
}
for (int i : {0, 1}) [
	cout << i << endl;
}
```

# Streams

Three base classes that make up all functions and other classes that work with
reading or writing to streams: `istream`, `ostream`, `iostream`.

To work with file stream include `<fstream>` module:
+ `ifstream` - reading from files (inherits from `istream`)
+ `ofstream` - writing to files (inherits from `ostream`)
+ `fstream` - reading/writing to/from files (inherits from `iostream`)

Reading from file example (`ifstream` costructor takes file path as constructor
argument; getline return reference to the stream, which can be casted to
boolean, false will indicate that EOF has been reached; `getline` takes optional
3rd parameter - delimiter, which is new line by default):
```c++
#include <iostream>
#include <fstream>
#include <string>

int main() {
	ifstream input("/path/to/file");
	string line;
	// checking if file stream was successfully opened
	// another way to do the same:
	// if (input)
	if (input.is_open()) {
		while (getline(input, line)) {
			cout << line << endl;
		}
	} else {
		cout << "error!" << endl;
	}
	return 0;
}
```

Writing to file example (`ofstream` constructor takes optional second argument
parameter to indicate writing mode - `ios::app` will open file in append mode):
```c++
#include <fstream>

int main() {
	ofstream output("/path/to/file");
	output << "hello" << endl;
	return 0;
}
```

To skip symbols in file use `ignore(number)` method on `ofstream` object, which
takes integer to indicate how many elements to skip.

## Manipulator

Manipulator switches the stream to work on a different mode. To work with
stream manipulator add `<iomanip>` module.

Available modes:
+ `setw(positive_integer)` - set the field width, take integer as parameter
+ `setfill(symbol)` - set sign to fill the free space set by `setw`
+ `left` - force left side allignment
+ `setprecision(positive_integer)` - set number of digits after dot for floats
+ `fixed` - force printing whole double instead of scientific notation, defaults
to 6 digits; `scientific` - scientific notation, default is set automatically
+ `hex` - changes stream to work in hexadecimal mode (`dec` is default, `oct` -
octal)
+ `setbase(positive_integer)` - directrly sets the base

Example:
```c++
#include <iomanip>
#include <iostream>

int main() {
	cout << setw(10);
	cout << "a" << endl;
	return 0;
}
```

## Read/Write operator overloading

```c+++
#include <iostream>
#include <sstream>
#include <iomanip>

struct Duration {
	int hour;
	int min;

	Duration (int h = 0, int m = 0) {
		hour = h;
		min = m;
	}
}

istream& operator>>(istream& stream, Duration& duration) {
	stream >> duration.hour;
	stream.ignore(1);
	stream >> duration.min;
	return stream;
}

ostream& opeator<<(ostream& stream, const Duration& duration) {
	stream << setfill('0)';
	stream << setw(2) << duration.hour << ':' << setw(2) << duration.min;
	return stream;
}

int main() {
	stringstream dur_ss("1:50");
	Duration duration;
	dur_ss >> duration;
	cout << duration << endl;
	return 0;
}
```

# Libraries

## iostream

All objects of iostream library are defined in std namespace. iostream library
doesnt provide a way to accept keyboard input without user having to press
enter.

`<<` - insertion operator, inserts a string of characters into the character
device. `cout` - standard output stream which outputs on the screen. Expression
is a valid cout element too.

Simple example and example with multiple arguments:
```c++
int sum = 441;
cout << sum;
cout << "total sum is " << sum;
```

    > cout is able to recognize the actual type of its elements:
        char Char = 'X';
        cout << Char << " " < (int)Char;
    > endl manipulator works just like adding '\n'

`>>` - extraction operator. `cin` - need to explicitely specify the variable
that can store the data. Recognized the input: int is converted through smth
like atoi, string as string, character - first entered character is saved.

Input is parsed via whitespaces. Example belows assigns `5` and `7` to variables
`x` and `y` when user enters line `5 7`:
```
int x, y;
cin >> x >> y;
```


## algorithm

Algorithm library contains functions to work with containers, like `min`, `max`,
and `sort`. The only requirement for listed functions is to have a `less than`
operator to the underlying data type.

A lot of functions take range as parameters, where built-in `begin()` and
`end()` can be used.

```c++
#include <algorithm>

bool Gt2 (int x) {
	if (x > 2) {
		return true;
	}
	return false;
}

int main() {
	vector<int> nums = {1, 5, 2, 5, 3};

	// sort elements:
	sort(begin(nums), end(nums));

	// count number of elements in a container:
	int quantity = count(begin(nums), end(nums), 5);
	cout << "found " << quantity << " fives";

	// count if argument satisfies the condition
	// custom function should return boolean
	quantity = count_if(begin(nums), end(nums), Gt2);
	cout << "found " << quantity << " greater than 2";

	int thr = 0;
	cin >> thr;
	// same as previous using lambda function
	// square brackets are used to pass variable from the context
	// lambda function is called, inside its bode
	quantity = count_if(begin(nums), end(nums), [thr](int x) {
		if (x > thr) {
			return true;
		}
		return false;
	});
	return 0;
}
```

## chrono

Allows user to work with timing periods.

Example on clocking parts of program execution:
```c++
#include <chrono>
using namespace std;
using namespace std:chrono;

void SomeFunction(int x);

int main(void) {
	auto start = steady_clock::now();
	SomeFunction(1);
	auto finish = steady_clock::now();
	cout << "duration "
		<< duration_cast<milliseconds>(finish - start).count() << ms;
	return 0;
}
```

# Functions

Function's return type defaults to `int`, while `void` means nothing. It is
allowed to have multiple functions of the same name (overloading). `void` in
parameter list indicated no parameters; every parameter has to have a type in
front of it.

`return;` can be placed in void function; implicitly executed within void
function's body. Result of void function can't be assigned to a variable, thus,
only acceptable invocation is `VoidFunction();`.

Global variable is the one declared outside any function, thus, acceptable by
all functions declared in the same source file after variable declaration;
functions can modify it (called side effect).

Passing parameters:
+ by value doesn't change actual parameter's value, as the later
only provides the value. Container passed by value result in sending deep copy.

+ by reference:
```c++
void function(int& parameter);
```
every modification made into a formal parameter will affect actual parameter;
corresponding actual parameter must be a variable - cant be expression or other
function invocation result
+ 'C language' way of passing by reference - pass pointer to a variable

`const` keyword can be used to declare that parameter won't be changed. Useful
when passing parameter by reference:
```c++
void function(const int& parameter);
```
This also allows passing function invocation as actual parameter. `const` also
extends to the element of containers, so one can't modify characters in the
vector of strings.

Default parameters
    > default parameter declaration:
        type function (type parameter = value){} - default is used when actual
            parameter is ommitted
    > non-default parameters must be coded first
    > parameters are assigned in the same order as they appear - thus cant assign
        default value for first and explicit value for the second

Inline functions
    > each function's code has to be supplemented with two elements:
        - prologue - implicitly executed before the function; transfers
            parameters from the invoker's code to function's stack
        - epilogue - implicitly executed after the function; transfers the
            result of the function and clears the stack
    > inline function - is written by compiler on every invocation (speeds up
        the program, good for short functions); can put inline either before or
        after type, in definition or declaration or both

Overloading
    > same name can be used for multiple functions, but must be distiguishable to
        the compiler:
        - number of parameters
        - parameter's types
    > return type isn't taken into account
    > 1.0 is literal of type double, thus if two overloading functions have int
        and float, compiler can't promote double to float (more precision to
        less one) and will cause a compilation error (won't find the best candidate)
    > ternary ? operator:
        expr1 ? expr2 : expr3 - 1 is evaluated, if result is non-zero 2 is returned,
            otherwise 3

# Exceptions

Exceptions have a base class `exception` that can be used to catch any
exceptions. Its `what()` method can be used to retrieve a message, if it has
one.

```c++
// throw general exception
void SomeFunction() {
	if (condition) {
		throw exception();
	}
}

// throw exception with a message
void SomeFunction() {
	if (condition) {
		string msg = "description";
		throw runtime_error(msg);
	}
}

int main() {
	try {
		SomeFunction();
	} catch (exception& ex) {
		cout << ex.what();
	}
}
```
