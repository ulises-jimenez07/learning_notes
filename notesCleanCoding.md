

1. Variables Nouns or short phrases with adjectives

```python
    const userDAta = {}
    const isValid = 
```

2. Functions / Methods Verbs or short phrases with adjectives

```python
    sendData()
    inputIsValid()
```

3. Classes use Nouns or short prhases with nouns
```python
    class User {...}
    class RequestBody {...}
```
# Casing

1. snake_case. Mostly in Python, Variables, functions and pmethos
   1. is_valid
   2. send_response
2. camelCase
   1. isValid
   2. sendResponse
3. PascalCase . For Classes
   1. AdminRole
   2. UserRepository
4. Kebab-case
   1. <side-drawer>


# Naming
- value, describe the value
- value number or string, decribe the value
- value is a boolean, question that can be answered yes or no

**Functions**
operations that it performs

```python
    validate_input()
    is_valid()
```

**Classes**

Describe the object
- User
- Product

Instances
User
Admin

SQLDatabase 

# Comments

Comments are usually bad, that has redundant information.

## Good Comments
1. Legal information
2. Explanation that cannot be added by naming
   1. Ex. Regular expressions
3. Warnings
4. TODO notes
5. Documentations comments

## vertical formatting
1. Avoid too many jumps. Keep files short, avoid multiple classes in the same file
2. **Spaces** related concepts together, 


# Functions

## Parameters
Minimize the number of parameters
1. None, easy to understand  and to call `user.save()`
2. 1, stil very easy `log(message)`
3. 2, Acceptable, adds order `Point(10, 20)`
4. 3, challenging to understand `calc(5, 10, "calc")`

Refactor using dictionaries or other structurec

## Body

They should be small. Split functions into smaller functions.

1. Functions should do exactly one thing
Highlevel, you know the meaning and the 
low Level, needs to have more cotext

When to split a function,
1. **Lines of code** that execute the same functionality
   ```python
    user.setAge(31)
    user.setName("John")

    user.update({"age": 31, "name": "John"  })
    ```
2. **Extract code** that requires more interpretation thatn the surronding
    ```python
    if(!email.includes('@'))
        saveNewUser(email)

    if(!isValid(email))
        saveNewUser(email)
   ))
    

- DRY (Don't Repeat Yourself)
- Dont split when renaming, or not able to find a good name
- Keep functions, Pure. For the same inputs, generates the same outputs. No side effects
Side 


# Control Structures

1. Avoid Deep Nesting
2. Using Functions and Polymorphisim
3. Prefer Positve Checks
4. Utilize Errors

## Guards

Guards and Fail Fast

Guards the rest of the code
```python

if email.includes("@"):
    #do stuff

# Fail Fast 
if !email.includes("@"):
    return
# Do stuff
```

Removes unncessary nesting

Throwing + handling errors can replace if statements and lead to more focu=s functions

simple rule: If somehing is an error -> make it an eror

```javascript
if(!isEmail){
    return {code: 442, messages:'Invalid Input'};
}

if(!isEmail){
    const error= new Error('Invalid error');
    error.code = 442;
    throw error;
}
```

Error handling is one thinkg

## Polymorphism

Object or functions that , produces different Factory functins. Different code
that executes on different situation

# Classes & Objects

**Objects**: Private internals, public API methods and real abstractions

**Data containers** Public internals. Store and transport data

## Polymorphism

Object of method that has the same name but does different tasks depending on the parameters.

When working. 

Clean classes, should be small. Multiple smaller classes (similar to functions). Classes should have a single responsability
SRP (Single Responsability Principle).
Small Focus classes.

## Cohesion
How much are class methods using the class properties?
Highly cohesive classes, most of the methods use the class propoerties.

## Law of Demeter
More maintanable code, 
Avoid code like this
``` java
    this.customer.lastPurchase.date;
```
Dont depned on the internals of stranges, other objects which you don't directly know.

Code in a methdo may only acces direct internals
- Objects it belongs to
- objtas that are stored in propertsis of that objetes
- objects which are recieved as method parameter
- obcjects which are created in the method

Tell, Don't ask principle.

## SOLID principles

- **Single Responsability Principle**
- **Open Closed Principle**
- Liskov Substitution Principle
- Interface Segregation Principle
- Dependency Inversion Principle

### Single Responsability Principle
Classes hould have a single responsability.


### The open closed principle

A class should be open for extension but closed for modification.
For example specific sub classes for each extension.


### The liskov substitution
Objects should be replaceable with instances of their sublcasses without altering the behavior

### Interface Segregation Principle
interfaces enforce certain behavior. 

Many client -specific interfaces are better than on general purpose interface.


### Dependency Inversion Principle
You should depend upon abstractions, not concretions.

# Summary
## Naming

- Choose descriptive names
- Nonos for variables and properites, nouns for clasess, verbs for methods
- Be specific if possible
- avoid slang, unkownw abreviation

## comments and formatting
- Most comments are baed
- vertical formatting, related conpets together and separate conceps taht are not closely related. 
- keep lines short


## Functions
- limit th enumber of parameters. dictionaries for examples
- clian functions should be small and do only one thing
- exploer leves of abstranctions
- DRY

## control structures
- Prefer positive workding
- avoid deep nesting. Use guards
- consider polymorphoismo and factory functions
- use real errors


## Objects
- Objects vs data structures
- Clean classes have one responsability
- SOLID principales