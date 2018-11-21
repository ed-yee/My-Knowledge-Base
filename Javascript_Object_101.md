# ES5 Class

## Simple Class Definition

The `class` construct doesn't exist until ES6. In ES5, a Javascript class is defined as a function.

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 */
function BaseClass() {

}

// Create an instance of BaseClass
var a = new BaseClass()
```

## Private and Public Properties and Methods

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 */
function BaseClass() {
    // Public property
    this.property1

    // Private property
    var propertyA
}

// Create an instance of BaseClass
var a = new BaseClass()

// Set and get property1 value
a.property1 = 'hello'
console.log(a.property1)

// This line doesn't give you error but it doesn't do anything either.
a.propertyA = 'world'
```

We added two properties, `property1` and `propertyA`. `property1` is declared as a public property - can read and write in a new instance. `propertyA` is declared as a private property - can only read and write within the class.

Similarily, you can define public and private functions/methods.

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 */
function BaseClass() {
    // Public property
    this.property1

    // Private property
    var propertyA

    // Public methods
    this.fn1 = function() {
        console.log('This is fn1. It sets a value in propertyA. showPropertyA() will show the value')
        propertyA = 'This is propertyA value'
    }

    // Another way to declare a public method
    function showPropertyA() {
        console.log(propertyA)
    }
    this.showPropertyA = showPropertyA

    // This is a private method
    function fn3() {
        console.log('This is fn3')
    }
}

// Create an instance of BaseClass
var a = new BaseClass()

// Set and get property1 value
a.property1 = 'hello'
console.log(a.property1)

// This line doesn't give you error but it doesn't do anything either.
a.propertyA = 'world'

// Call public method fn1
a.fn1()

// Call public method showPropertyA
a.showPropertyA()

// Call a private method. This gives you an error.
a.fn3()
```

## Initializing a Class Instance

Since Javascript class is actually a function therefore we can pass value as function parameters. It is also a constructor of a class instance.

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 * @param {number} param1
 * @param {string} param2
 * @param {boolean} param3
 */
function BaseClass(param1, param2, param3) {
    // Public property
    this.property1 = param1
    this.property2 = param2
    this.property3 = param3
}

var a = new BaseClass(1, 'hello', true)
console.log(a.property1) // Shows 1
console.log(a.property2) // Shows 'hello'
console.log(a.property3) // Shows true
```

What if I don't want to initialize the second parameter?

``` Javascript
var a = new BaseClass(1, undefined, true)
```

What if I want to have a default value to all or some parameters?

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 * @param {number} [param1=100]
 * @param {string} [param2='HelloWorld']
 * @param {boolean} [param3=false]
 */
function BaseClass(param1 = 100, param2 = 'Hello World', param3 = false) {
    // Public property
    this.property1 = param1
    this.property2 = param2
    this.property3 = param3
}

var a = new BaseClass(1, undefined, true)
console.log(a.property1) // Shows 1
console.log(a.property2) // Shows 'Hello World'
console.log(a.property3) // Shows true
```

If you have a long list of parameters in the constructor, you may want to change it to an object literal.

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 * @param {Object} initialValues
 */
function BaseClass(initialValues = { param1: 100, param2: 'Hello World', param3: false }) {
    // Public property
    this.property1 = initialValues.param1
    this.property2 = initialValues.param2
    this.property3 = initialValues.param3
}

var a = new BaseClass()
console.log(a.property1) // Shows 100
console.log(a.property2) // Shows 'Hello World'
console.log(a.property3) // Shows false

var b = new BaseClass({ param1: 1, param3: true })
console.log(b.property1) // Shows 1
console.log(b.property2) // Shows undefined
console.log(b.property3) // Shows true
```

In the above example, `b.property2` shows `undefined`. Since the new default value is an object literal. Javascript will apply all default values or none of it. How can we fix that?

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 * @param {Object} initialValues
 */
function BaseClass(initialValues = { param1: 100, param2: 'Hello World', param3: false }) {
    // Public property
    this.property1 = initialValues.param1 || 100
    this.property2 = initialValues.param2 || 'Hello World'
    this.property3 = initialValues.param3 || false
}

var a = new BaseClass()
console.log(a.property1) // Shows 100
console.log(a.property2) // Shows 'Hello World'
console.log(a.property3) // Shows false

var b = new BaseClass({ param1: 1, param3: true })
console.log(b.property1) // Shows 1
console.log(b.property2) // Shows 'Hello World'
console.log(b.property3) // Shows true
```

Now we have all the default values

## Alternative Way to Define Properties - Using Getter and Setter

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 * @param {string} firstName
 * @param {string} lastName
 */
function BaseClass(firstName = '', lastName = '') {
    this.firstName = firstName
    this.lastName = lastName
    this.fullName = firstName + ' ' + lastName
}

var a = new BaseClass('John', 'Doe')
console.log(a.firstName) // Shows 'John'
console.log(a.lastName) // Shows 'Doe'
console.log(a.fullName) // Shows 'John Doe'

var b = new BaseClass()
b.firstName = 'Peter'
b.lastName = 'Pan'
console.log(b.firstName) // Shows 'Peter'
console.log(b.lastName) // Shows 'Pan'
console.log(b.fullName) // Shows nothing

var c = new BaseClass('Buzz', 'Lightning')
c.fullName = 'Angry Bird'
console.log(c.firstName) // Shows 'Buzz'
console.log(c.lastName) // Shows 'Lightning'
console.log(c.fullName) // Shows 'Angry Bird'
```

In the above example, `b` instance `fullName` doesn't show anything. The `fullName` property is a "computed property". Its value is based on some code logic. The `fullName` is populated during class instantiation. That's not good.

Furthermore, the `fullName` property can be overwritten as in `c` instance.

So we need a way to make property `fullName` a read-only computed-property.

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 * @param {string} firstName
 * @param {string} lastName
 */
function BaseClass(firstName = '', lastName = '') {
    this.firstName = firstName
    this.lastName = lastName

    Object.defineProperties(this, {
        'fullName': {
            get: function() {
                return firstName + ' ' + lastName
            }
        }
    })
}

var a = new BaseClass('John', 'Doe')
console.log(a.firstName) // Shows 'John'
console.log(a.lastName) // Shows 'Doe'
console.log(a.fullName) // Shows 'John Doe'

var b = new BaseClass()
b.firstName = 'Peter'
b.lastName = 'Pan'
console.log(b.firstName) // Shows 'Peter'
console.log(b.lastName) // Shows 'Pan'
console.log(b.fullName) // Shows nothing

var c = new BaseClass('Buzz', 'Lightning')
c.fullName = 'Angry Bird'
console.log(c.firstName) // Shows 'Buzz'
console.log(c.lastName) // Shows 'Lightning'
console.log(c.fullName) // Shows 'Buzz Lightning'
```

We fixed instance `c`. We cannot change the `fullName` property. 

However, instance `b` is still not showing the full name. That is because the `fullName` property reflecting what was initialized with. To fix this, the code needs to define `firstName` and `lastName` using `Object.defineProperties`.

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 * @param {string} firstName
 * @param {string} lastName
 */
function BaseClass(firstName = '', lastName = '') {
    var fields = {
        firstName: firstName || '',
        lastName: lastName || ''
    }

    Object.defineProperties(this, {
        'firstName': {
            get: function() {
                return fields.firstName
            },
            set: function(value) {
                fields.firstName = value
            }
        },
        'lastName': {
            get: function() {
                return fields.lastName
            },
            set: function(value) {
                fields.lastName = value
            }
        },
        'fullName': {
            get: function() {
                return fields.firstName + ' ' + fields.lastName
            }
        }
    })
}

var a = new BaseClass('John', 'Doe')
console.log(a.firstName) // Shows 'John'
console.log(a.lastName) // Shows 'Doe'
console.log(a.fullName) // Shows 'John Doe'

var b = new BaseClass()
b.firstName = 'Peter'
b.lastName = 'Pan'
console.log(b.firstName) // Shows 'Peter'
console.log(b.lastName) // Shows 'Pan'
console.log(b.fullName) // Shows 'Peter Pan'
b.firstName = 'Michael'
b.lastName = 'Jordan'
console.log(b.fullName) // Shows 'Michael Jordan'

var c = new BaseClass('Buzz', 'Lightning')
c.fullName = 'Angry Bird'
console.log(c.firstName) // Shows 'Buzz'
console.log(c.lastName) // Shows 'Lightning'
console.log(c.fullName) // Shows 'Buzz Lightning'

```

Finally, it is all fixed. Instance `b` shows that we can change the `firstName` and `lastName` all the time, and `fullName` will reflect the changes.

The `get` and `set` are also known as getter and setter.

## Class Inheritance

Our sample `BaseClass` has first-name, last-name, and computed full-name.

One day, we want to include age but we don't want to break the existing app. What can we do?

We need to extend the `BaseClass`. That includes "things" defined in the `BasedClass`. The extended class is called `SubClass`.

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 * @param {string} firstName
 * @param {string} lastName
 */
function BaseClass(firstName = '', lastName = '') {
    var fields = {
        firstName: firstName || '',
        lastName: lastName || ''
    }

    Object.defineProperties(this, {
        'firstName': {
            get: function() {
                return fields.firstName
            },
            set: function(value) {
                fields.firstName = value
            }
        },
        'lastName': {
            get: function() {
                return fields.lastName
            },
            set: function(value) {
                fields.lastName = value
            }
        },
        'fullName': {
            get: function() {
                return fields.firstName + ' ' + fields.lastName
            }
        }
    })
}

function SubClass(firstName = '', lastName = '', age = 1) {
    BaseClass.call(this, firstName, lastName)

    var fields = {
        age: age || 1
    }

    Object.defineProperties(this, {
        'age': {
            get: function() {
                return fields.age
            },
            set: function() {
                fields.age
            }
        }
    })
}

var a = new SubClass('John', 'Doe', 12)
console.log(a.firstName) // Shows 'John'
console.log(a.lastName) // Shows 'Doe'
console.log(a.fullName) // Shows 'John Doe'
console.log(a.age) // Shows 12
```

In Javascript, there is an `instanceof` operator. It can be used to determine a given object instance is an implementation of a particular class.

``` Javascript
a instanceof SubClass // Shows true
```

In our example, the `SubClass` extends from `BaseClass`. So `a` should also be an instance of `BaseClass`.

``` Javascript
a instanceof BaseCase // Shows false
```

If you enter `a` in a Chrome console, it shows `a` is an implementation of `SubClass`. There is no indication of `BaseClass`. The `__proto__` indicates it is an `Object`. To Fix it, we need to link `Subclass` to `BaseClass`.

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 * @param {string} firstName
 * @param {string} lastName
 */
function BaseClass(firstName = '', lastName = '') {
    var fields = {
        firstName: firstName || '',
        lastName: lastName || ''
    }

    Object.defineProperties(this, {
        'firstName': {
            get: function() {
                return fields.firstName
            },
            set: function(value) {
                fields.firstName = value
            }
        },
        'lastName': {
            get: function() {
                return fields.lastName
            },
            set: function(value) {
                fields.lastName = value
            }
        },
        'fullName': {
            get: function() {
                return fields.firstName + ' ' + fields.lastName
            }
        }
    })
}

function SubClass(firstName = '', lastName = '', age = 1) {
    BaseClass.call(this, firstName, lastName)

    var fields = {
        age: age || 1
    }

    Object.defineProperties(this, {
        'age': {
            get: function() {
                return fields.age
            },
            set: function() {
                fields.age
            }
        }
    })
}
SubClass.prototype = Object.create(BaseClass.prototype)

var a = new SubClass('John', 'Doe', 12)
console.log(a.firstName) // Shows 'John'
console.log(a.lastName) // Shows 'Doe'
console.log(a.fullName) // Shows 'John Doe'
console.log(a.age) // Shows 12
```

Now, if you type `a` in the console, you will see `__proto__` referencing `BaseClass`, and the `instanceof` will work properly.

``` Javascript
a instanceof SubClass // Show true
a instanceof BaseClass // Show true
```

Later on, you want to further extend `SubClass` to include gender. You just need to do that same thing.

``` Javascript
a instanceof SubSubClass // Show true
a instanceof SubClass // Show true
a instanceof BaseClass // Show true
```

So far, our code inherits properties. What about methods?

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 * @param {string} firstName
 * @param {string} lastName
 */
function BaseClass(firstName = '', lastName = '') {
    var fields = {
        firstName: firstName || '',
        lastName: lastName || ''
    }

    Object.defineProperties(this, {
        'firstName': {
            get: function() {
                return fields.firstName
            },
            set: function(value) {
                fields.firstName = value
            }
        },
        'lastName': {
            get: function() {
                return fields.lastName
            },
            set: function(value) {
                fields.lastName = value
            }
        },
        'fullName': {
            get: function() {
                return fields.firstName + ' ' + fields.lastName
            }
        }
    })

    /**
     * Validate class instance
     * @returns {boolean} True if both first and last names are not empty
     */
    function isValid() {
        return fields.firstName.length > 0 && fields.lastName.length > 0
    }
    this.isValid = isValid
}

function SubClass(firstName = '', lastName = '', age = 1) {
    BaseClass.call(this, firstName, lastName)

    var fields = {
        age: age || 1
    }

    Object.defineProperties(this, {
        'age': {
            get: function() {
                return fields.age
            },
            set: function() {
                fields.age
            }
        }
    })
}
SubClass.prototype = Object.create(BaseClass.prototype)

var a = new SubClass('John', 'Doe', 12)
a.isValid()
```

So far an instance is valid if the first and last name are not empty. What if the rule is changed first and last name are not empty and the age is more than 10?

We need to add a validation method to the `SubClass`.

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 * @param {string} firstName
 * @param {string} lastName
 */
function BaseClass(firstName = '', lastName = '') {
    var fields = {
        firstName: firstName || '',
        lastName: lastName || ''
    }

    Object.defineProperties(this, {
        'firstName': {
            get: function() {
                return fields.firstName
            },
            set: function(value) {
                fields.firstName = value
            }
        },
        'lastName': {
            get: function() {
                return fields.lastName
            },
            set: function(value) {
                fields.lastName = value
            }
        },
        'fullName': {
            get: function() {
                return fields.firstName + ' ' + fields.lastName
            }
        }
    })

    /**
     * Validate class instance
     * @returns {boolean} True if both first and last names are not empty
     */
    function isValid() {
        return fields.firstName.length > 0 && fields.lastName.length > 0
    }
    this.isValid = isValid
}

function SubClass(firstName = '', lastName = '', age = 1) {
    BaseClass.call(this, firstName, lastName)

    var fields = {
        age: age || 1
    }

    Object.defineProperties(this, {
        'age': {
            get: function() {
                return fields.age
            },
            set: function() {
                fields.age
            }
        }
    })

    function isValid() {
        return fields.age > 10
    }
    this.isValid = isValid
}
SubClass.prototype = Object.create(BaseClass.prototype)

var a = new SubClass('John', 'Doe', 12)
console.log('a is valid?', a.isValid()) // Show true

var b = new SubClass(undefined, undefined, 12)
console.log('b is valid?', b.isValid()) // Show true
```

In the above example, `b.isValid()` is actually returning true!! That can't be right.

As it turns out, `SubClass.isValid()` doesn't validate the first and last name. We could add them to the `SubClass.isValid()` like this...

``` Javascript
function isValid() {
    return fields.age > 10 && this.firstName.length > 0 && this.lastName.length > 0
}
```

This works. 

> Note that how the `isValid` function referencing `firstName`, `lastName`, and `age`. >`firstName` and `lastName` are defined in the BaseClass. They are inherited into the > `SubClass` but can only reference using `this.` - Object properties.
>
> `fields.age` references a private variable. We can drop the `fields.` and that becomes an object property. `this.age` is also referring to an object property. 
>
> It is recommended to use either `fields.age` or `this.age` - less confusion.

However, what if we have a very complex rule? That makes the `SubClass.isValid()` very long and also duplicating code in the `BaseClass`. Furthermore, if we change the `BaseClass.isValid()` definition, we also need to modify all sub-classes. That's no good!

We need to be able to call the `BaseClass.isValid()`.

To do that we need to add BaseClass.isValid() in the prototype chain.

``` Javascript
function BaseClass(firstName = '', lastName = '') {
    ...
    function isValid() {
        ...
    }
    BaseClass.prototype.isValid = this.isValid = isValid
}
```

To invoke the `BaseClass.isValid` from `SubClass.isValid()`, we need to `call` it.

``` Javascript
function SubClass(firstName = '', lastName = '', age = 1) {
    BaseClass.call(this, firstName, lastName)
    ...
    function isValid() {
        if (BaseClass.prototype.isValid.call(this)) {
            ...
        }
    }
    this.isValid = isValid
}
```

Here is a complete example:

``` Javascript
/**
 * BaseClass class definition
 * @constructor
 * @param {string} [firstName='']
 * @param {string} [lastName='']
 */
function BaseClass(firstName = '', lastName = '') {
    var fields = {
        firstName: firstName || '',
        lastName: lastName || ''
    }

    Object.defineProperties(this, {
        'firstName': {
            get: function() {
                return fields.firstName
            },
            set: function(value) {
                fields.firstName = value
            }
        },
        'lastName': {
            get: function() {
                return fields.lastName
            },
            set: function(value) {
                fields.lastName = value
            }
        },
        'fullName': {
            get: function() {
                return fields.firstName + ' ' + fields.lastName
            }
        }
    })

    /**
     * Validate class instance
     * @returns {boolean} True if both first and last names are not empty
     */
    function isValid() {
        return this.firstName.length > 0 && this.lastName.length > 0
    }
    BaseClass.prototype.isValid = this.isValid = isValid
}

/**
 * SubClass class definition
 * @constructor
 * @param {string} [firstName='']
 * @param {string} [lastName='']
 * @param {number} [age=1]
 */
function SubClass(firstName = '', lastName = '', age = 1) {
    BaseClass.call(this, firstName, lastName)

    var fields = {
        age: age || 1
    }

    Object.defineProperties(this, {
        'age': {
            get: function() {
                return fields.age
            },
            set: function() {
                fields.age
            }
        }
    })

    /**
     * Validate object instance
     * @return {boolean} True if first and last name are not empty and age is greater than 10
     */
    function isValid() {
        if (BaseClass.prototype.isValid.call(this)) {
            if (this.age > 10) {
                return true
            }
        }

        return false
    }
    this.isValid = isValid
}
SubClass.prototype = Object.create(BaseClass.prototype)

var a = new SubClass('John', 'Doe', 12)
console.log('a is valid?', a.isValid()) // Show true

var b = new SubClass(undefined, undefined, 12)
console.log('b is valid?', b.isValid()) // Show false

var c = new SubClass('Peter', 'Pan', 15)
console.log('c is valid?', c.isValid()) // Show true

var d = new SubClass('Peter', 'Pan', 3)
console.log('d is valid?', d.isValid()) // Show false
```
