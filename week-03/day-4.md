Day 4
======================
### Video Resources (India Platoon)
- [Week 3 Videos](https://www.youtube.com/playlist?list=PLu0CiQ7bzwERYl9BQgqCObTzijFbd73Oe)

### Challenges
* [School Interface Three](https://github.com/codeplatoon/school-interface-three)
* [Bank Accounts](https://github.com/codeplatoon/bank-accounts)

### Lecture Topics
* Object-Oriented Programming vs. Functional Programming
* Python Modules 

### Object-Oriented Programming vs. Functional Programming
There are generally two programming paradigms that people follow: functional programming and object-oriented programming. However loud the proponents are on both sides of the aisle, there is not a clear "winner" - it depends largely on the scale of your project, the skill level of your developers, and what you're trying to accomplish with your code. Today, we're going to take a closer look into both types of coding and see when each one shines.

Functional programming has surged in popularity in recent years due to its increased speed compared to object-oriented programming. In FP, we avoid changing state, meaning that the functions/methods we write don't impact _anything_ outside the function. There aren't any instance variables and we don't change anything about any objects. It's simply X going in and Y coming out. An example:

```python
def add(number_1, number_2):
    print(number_1 + number_2)
```

In this example, I've got 2 numbers that I pass into the `add` method. The result is the sum of those numbers. This is what's known as a "pure" function - it always produces the same output from your input and has no side effects. I can just as easily rewrite this as the following and get the same result:

```python
def add(number_1, number_2):
    number_2 + number_1
```

Most of the code we've been writing thus far with our algorithms have been functional in that we provide our functions with parameters and get back a result. We've not really been working objects, keeping track of state, etc. That being said, we're going to be mostly working in the object-oriented paradigm soon.

In object-oriented programming, we organize our code into reusable classes to create new `objects`. Each object is loaded into our computer's memory and each  object keeps track of its own state. For example:

```python
class Account:
    def __init__(self, balance):
        self.balance = balance

    def deposit(self, amount):
      self.balance += amount

account = Account(100)
print(account.balance)
account.deposit(50)
print(account.balance)
```

In the above example, we have an Account object that keeps track of its own balance. Our function `deposit` takes in an argument and pushes out an output, but it also alters the state of the object's `balance`. Thus, it is an `impure` function and ultimately, object oriented code.

Objected oriented code is not evil and is far more popular to program with than functional programming. For most companies and for most projects, you'll want to organize your code into objects so that you can quickly reference/alter its attributes and call its methods to update things in your database.

### Python Modules
Using modules in Python allow for you as the author to organize your code and group it together for ease of use. To put it very simply, a module is a file of Python code that you bring into other files. Let's take a look at an example:
```python
# file1.py
def say_hello():
    print('Hey there')


# file2.py
import file1
import file1 as anything
anything.say_hello()
```
We just created two files: `file1.py` and `file2.py`. `file1.py` has a `say_hello` function. `file2.py` has nothing in it, but imports that file in as the name of the file and we can use all the methods in that file. We can also rewrite it as `import file1 as anything` and call `anything.say_hello()`. You can `import` anything into your file by providing the correct relative path to the file. More on that can be found [here](https://www.tutorialspoint.com/python/python_modules.htm)

### Homework
* Finish Bank Accounts
* Read [Functional vs. OOP](https://www.codenewbie.org/blogs/object-oriented-programming-vs-functional-programming)
