# INTRODUCTION

JavaScript is a programming language that uses **lexical scope** with **function scope** to define the variables values,
except for the **THIS** keyword, which is a variable whos value seems to change strangely and that we cannot control.

The reason to this behavior is that the object **THIS** has some kind of **dynamic scope** to define it's value, 
that's why on execution time **THIS** changes to values that we didn't defined in our code.

The changes to **THIS** values are due to the close relationship between the **THIS** object and the **execution context** (_enviroment or 
scope in which a line of code is being executed_) in JavaScript. 
During the **execution time** of one code line of a JavaScript program, the **execution context** changes taking into consideration the owner object 
of the function being call and **THIS** stores a reference to this owner object.

It's important to understand how the value of **THIS** changes because sometimes it may seem that has one value but it changes to another, 
for example: a function being call inside an object's method.

----
```
  let person = {
    name:"Carlos",
    talk: function(){
      let text= 'Mi name is';
      function innerfunction(){
        console.log(`${text} + ${this.name}`);
      }
      innerfunction();
    }
  }
  person.talk() //Output: Mi names is +
```
----
As mention before the execution context changes at execution time due to the function being call by it's owner object.
Since a function can be call diferent ways (implicit, explicit, event listenes, others.), the same function can have diferent execution context, 
whichs means a function can have diferents values for **THIS** keyword. 

An easy way to know the value of **THIS** is to determine how the function that contains the **THIS** keyword was call at execution time. 
It helps to answer the question: Who call the function?

In execution time who can call a function to execute are (posibles values of **THIS**): 
- the **Global object** (window if we are working on a browser, global object on NodeJS).
- objects (a define structure with methods and properties).
- DOM elements.
- primitives data types (string, boolean, number, bigint, undefined, symbol). 

In JavaScript is not posible to alter how the lexical scope works, but it is possible to control the execution context in which we call our functions. 
We can control and fixed the value of **THIS** with: **clousure**, **call**, **apply**, **bind** and **arrow functions**.

----
```
  function boo(){ 
    return this+13
  }
  function foo(){ 
    return typeof this
  }
  function buzz(){ 
    return this
  }
  function too(){ 
    return `${typeof this} 13`
  }
  var roles = Symbol("rol")
  boo.call(1)// 14
  boo.call("12")// "1213"
  boo.call(true)// 14
  boo.call(false)// 13
  boo.call(0.3)// 13.3
  typeof roles// "symbol"
  boo.call(roles)// Uncaught TypeError: Cannot convert a Symbol value to a number
  foo.call(roles)// "object"
  buzz.call(roles)// Symbol {Symbol(rol)}
  too.call(roles)// "object 13"
```
----
### Now that we know the **THIS** object it is important to understand how it is bind to the scope: :pushpin:
 - [Default(window)](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#this-default-binding)
 - [new keyword](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#this-new-binding)
 - [Lexical](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#this-lexical-context-binding)
 - [implicit](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#this-implicit-binding)
 - [explicit](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#this-explicit-binding)
               
# **THIS** Default binding
In the **Global Execution Context**, **THIS** refers to the **Global Object**, if we set **strict mode** on **THIS** value is undefined,
however we can still refer to the **Global Object** via the global property **globalThis**.

----
```
  this.hola="hola"
  function Global(){
    'use strict'
    var that=globalThis
    that.hola="BEBE"
    return that;
  }
  console.log(Global())
```
----
The **THIS** value in the **global scope** refers to the **Global Object** (Window).

----
```
  this.table='window table'
  this.garage={
    table:'garage table'
  }
  let johnsRoom = {
    table:'johnsTable'
  }
  console.log(window.table)// window table
  console.log(window.garage.table)// garage table
  console.log(this.garage.table)// garage table
  console.log(this.johnsRoom.table)// Error (Note 1)
  /*Note 1
    the object "johnsRoom" is a private property since the **LET** keyword was used if the **VAR** keyword is use instead then it will
    possible the access to the property this way.
  */
```
----
When a function is invoke as a functions **THIS**'s value refers to the **Global Object** (Window).

----
```
  function test(){
    console.log(this === window)
  }
  console.log(this === window)// true
  test()// true
``` 
----
When belonging to the **global context** the **THIS** value can be change outside the function, their's no data encapsuling.

----
```
  const TellAge = function(){
    console.log(this.age)
  }
  TellAge()// undefined
  window.age = 22
  TellAge()// 22
```
----
For correcting this behavior the strict mode is use. A function which has **'use strict'** in their scope the value **THIS** will be **undefined** instead 
of the **window** object.

----
```
  const TellAge2 = function(){
    'use strict'
    console.log(this.age)
  }
  TellAge2()// undefined
  window.age = 22
  TellAge2()// Uncaught TypeError: Cannot read property 'age' of undefined at TellAge2
```
----
[return to :pushpin:](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#now-that-we-know-the-this-object-it-is-important-to-understand-how-it-is-bind-to-the-scope-pushpin)
# **THIS** new binding
The **NEW** keyword is use together with **construction functions** (a function use to create object) assigning it to a variable to create objects. In which **THIS** value is 
refers to the new created instance.

----
```
  const Person = function(fn, ln){
    this.first_name = fn;
    this.last_name = ln;
    this.displayName = function(){
      console.log(`Name: ${this.first_name} ${this.last_name}`)
    }
  }
  let mom = new Person("Joceline","Aguilar")
  let dad = new Person("Noel", "Soto")
  mom.displayName()//Name: Joceline Aguilar
  dad.displayName()//Name: Noel Soto 
  mom===dad// false
```
----
Example 2

----
```
  'use strict'
  const createRoom=function(name){
    this.table=`${name}'s table`
  }
  createRoom.prototype.cleanTable = function(soap){
    console.log(`cleaning ${this.table} using ${soap}`)
  }

  let jillsRoom = new createRoom('jill')
  let johnsRoom = new createRoom('john')
  jillsRoom.cleanTable('some soap')// cleaning jill's table using some soap
  johnsRoom.cleanTable("Zest's soap")// cleaning john's table using Zest's soap
```
----
Example 3

----
```
  function C(){
    this.a=37
  }
  function C2(){
    this.a=37
    return {a:30}
  }
  var C = new C();
  var C2 = new C2()
  console.log(C.a)// 37
  console.log(C2.a)// 30 
```
----
An object constructor function has the problem that if you omit the new keyword the behavior is modify and doesn't show any errors.

----
```
  var r="red"
  function Color(r,g,b){
    this.r=r;
    this.g=g;
    this.b=b;
  }
  var colors1 = new Color("red apple", "green eyes", "blue sky")
  var colors2 = Color("red car", "green arrow", "blue beatle")
  console.log(colors1.r)//red apple
  console.log(r)//red car
  console.log(colors2.r)//Uncaught TypeError: Cannot read property 'r' of undefined
```  
----
A better approch to handdle this data protection integrety issue one solution would be.

----
```
  var r="red"
  function Color(r,g,b){
    if(this instanceof Color){
      this.r=r;
      this.g=g;
      this.b=b;
    }else{
      throw new Error("Missing new keyword")
    }
  }
  var colors1 = new Color("red apple", "green eyes", "blue sky")
  var colors2 = Color("red car", "green arrow", "blue beatle")
  console.log(colors1.r)//red apple
  console.log(r)//red car
  console.log(colors2.r)//Uncaught Error: Missing new keyword at Color
```  
----
[return to :pushpin:](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#now-that-we-know-the-this-object-it-is-important-to-understand-how-it-is-bind-to-the-scope-pushpin)
# **THIS** Lexical Context binding

----
```  
  function f1(){
    return this
  }
  function f2(){
    'use strict'
    return this
  }
  console.log(f1()===window)// true
  console.log(f2()===undefined)// true
  console.log(f1()===f2())// false
```  
----
In regular functions every new function defined has its own **THIS** keyword value, which owner is the global object (window), except if the
function uses "strict mode" in which case is set to "undefined". The **THIS** keyword is not very useful in functions, where they really shine
is when is use with objects.

----
```
  var myApp = function(){
    var name = 'World'
    var sayHello = function(){
      console.log('Hello, ' + this.name)
    }
    sayHello()
  }
  myApp()// Hello
  name = 'Joceline'
  var myApp2 = function(){
    var name = 'Carlos Noel'
    var sayHello = function(){
      console.log('Hello, ' + this.name)
    }
    sayHello();
  }
  myApp2()// Hello Joceline
  function multiply(p, q, callback){
    callback(p*q)
  }
  let user = {
    a:2,
    b:3,
    findMultiply: function(){
      multiply(this.a, this.b, function(total){
        console.log(total)
        console.log(this===window)
      })
    }
  }
  user.findMultiply();// true (Note 1)
  /*Note 1*
    Since the "callback" is invoke like a simple function call inside a function, **THIS** refers to the Global Object.
    **THIS** inside a function
  */
```  
----
Using **THIS** in a function with 'strict mode'

----
```
  'use strict'
  this.table='window table'
  const cleanTable = function(){
    console.log(`cleaning ${this}`)
    function innerfunction(){
      console.log(`THIS value: ${this} `)
    }
    innerfunction()
  }
  cleanTable();// cleaning undefined THIS value: undefined
  /* Note
    The 'strict mode' permeates to inner functions scope
  */
```  
----
Using **THIS** in an inner function
Each function possess its own relation with **THIS** even inner functions. And by default **THIS**'s value is the "Global Object".
In the next example, we bind the **THIS** value with the **CALL** function but then its change to "undefined" (because the "strict mode" is "ON")
in the inner function. This is an unwanted behavior. (When a function changes the **THIS** value is know as a function loosing its context).

----
```
  'use strict'
  this.table='window table'
  this.garage={
    table:'garage table'
  }
  let johnsRoom={
    table:'Johns Table'
  }
  const outtercleanTable = function(soap){
    const innerfunction = function(_soap){
          console.log(`cleaning ${this.table} ${_soap}`)
    }
    innerfunction(soap);
  }
  outtercleanTable.call(this,'some soap');// Uncaught TypeError: Cannot read property 'table' of undefined
  outtercleanTable.call(this.garage,'some soap');// Uncaught TypeError: Cannot read property 'table' of undefined
  outtercleanTable.call(johnsRoom,'some soap');// Uncaught TypeError: Cannot read property 'table' of undefined
```  
----

For the example above where are three solutions:
  -A) closures
  -B) call, apply, and bind
  -C) arrow functions

A) **THIS** with closures is not a very good solution

----
```
  'use strict'
  this.table='window table'
  this.garage={
    table:'garage table'
  }
  let johnsRoom={
    table:'Johns Table'
  }
  const outtercleanTable = function(soap){
    let that = this
    const innerfunction = function(_soap){
          console.log(`cleaning ${that.table} ${_soap}`)
    }
    innerfunction(soap);
  }
  outtercleanTable.call(this,'some soap');// cleaning window table some soap
  outtercleanTable.call(this.garage,'some soap');// cleaning garage table some soap
  outtercleanTable.call(johnsRoom,'some soap');// cleaning Johns Table some soap
```  
----
B) **CALL**, **APPLY** and **BIND** approch

----
```
  'use strict'
  this.table='window table'
  this.garage={
    table:'garage table'
  }
  let johnsRoom={
    table:'Johns Table'
  }
  const outtercleanTable = function(soap){
    const innerfunction = function(_soap){
          console.log(`cleaning ${this.table} ${_soap}`)
    }    
    const myBindFunction = innerfunction.bind(this,soap);
    myBindFunction();
    innerfunction.call(this,soap);
    innerfunction.apply(this,[soap]);
  }
  outtercleanTable.call(this,'some soap');// cleaning window table some soap cleaning window table some soap cleaning window table some soap
  outtercleanTable.call(this.garage,'some soap');// cleaning garage table some soap cleaning garage table some soap cleaning garage table some soap
  outtercleanTable.call(johnsRoom,'some soap');// cleaning Johns Table some soap cleaning Johns Table some soap cleaning Johns Table some soap
```  
----
C) Arrow functions
Unlike regular functions, **arrow functions** don't have their own **THIS** keyword, it uses the **OUTTER SCOPE**'s **THIS** value. In the next
example the "innefunction" takes the "outterfunction"'s **THIS** which was set with the **CALL** function.

----
```
  'use strict'
  this.table='window table'
  this.garage={
    table:'garage table'
  }
  let johnsRoom={
    table:'Johns Table'
  }
  const outtercleanTable = function(soap){
    const innerfunction = (_soap)=>{
          console.log(`cleaning ${this.table} ${_soap}`)
    }    
    innerfunction();
  }
  outtercleanTable.call(this,'some soap');// cleaning window table some soap
  outtercleanTable.call(this.garage,'some soap');// cleaning garage table some soap 
  outtercleanTable.call(johnsRoom,'some soap');// cleaning Johns Table some soap 
```  
----
Example 3

----
```  
  class NameField{
    constructor(name){
      let ul = document.querySelector("idulcollegues")
      const li = document.createElement('li')
      li.textContent = name
      ul.appendChild(li)
    }
  }
  class NameGenerator{
    constructor(){
      const btn = document.querySelector("button");
      btn.addEventListener("click", this.addName);// *Note 3
    }
    addName(){
      const name = new NameField('Max');
    }
  }
  const test = new NameGenerator();
  /* Note 3
    this.addName is a reference to the function. If parenthesis were add the function will execute during parsing.
  */
```  
----
In the above example we see that for invoking the "addName" method from the "NameGenerator" class inside a constructor function we use
the **THIS** keyword, due to the method being added to the class. This means that **THIS** is referring to the class. One way to change 
**THIS** value is via 'Event Listener'.

----
```  
  class NameField{
    constructor(name){
      let ul = document.querySelector("idulcollegues")
      const li = document.createElement('li')
      li.textContent = name
      ul.appendChild(li)
    }
  }
  class NameGenerator{
    constructor(){
      this.names=['Max','Manu','Ana']
      this.counter=0
      const btn = document.querySelector("button");
      btn.addEventListener("click", this.addName);
    }
    addName(){
      const name = new NameField(this.name[this.counter]);
      this.counter++;
      (this.counter >= this.names.length) && (this.counter=0)
    }
  }
  const test = new NameGenerator();//Cannot read property 'undefined' of undefined
```  
----
In the example above when we press the "button" it show us the "Cannot read ..." message because the "button" modifies the **THIS** value.
In the class's constructor lexical scope **THIS** refers to the class an that's why it was able to find the "addName" method, but when the 
button is press **THIS** value changes because the one "who call the function" was the button not the object instance of the class 
'NameGenerator'. And the button doesn't has the properties "name" and "counter". We can correct this error using the **BIND** function.

----
```  
  class NameField{
    constructor(name){
      let ul = document.querySelector("idulcollegues")
      const li = document.createElement('li')
      li.textContent = name
      ul.appendChild(li)
    }
  }
  class NameGenerator{
    constructor(){
      this.names=['Max','Manu','Ana']
      this.counter=0
      const btn = document.querySelector("button");
      btn.addEventListener("click", this.addName.bind(this));
    }
    addName(){
      const name = new NameField(this.name[this.counter]);
      this.counter++;
      (this.counter >= this.names.length) && (this.counter=0)
    }
  }
  const test = new NameGenerator();
```  
----
Example 4 
In the next example use arrow function to keep the lexical context of **THIS**.

----
```  
  class NameField{
    constructor(name){
      let ul = document.querySelector("idulcollegues")
      const li = document.createElement('li')
      li.textContent = name
      ul.appendChild(li)
    }
  }
  class NameGenerator{
    constructor(){
      this.names=['Max','Manu','Ana']
      this.counter=0
      const btn = document.querySelector("button");
      btn.addEventListener("click", () => this.addName());
    }
    addName(){
      const name = new NameField(this.name[this.counter]);
      this.counter++;
      (this.counter >= this.names.length) && (this.counter=0)
    }
  }
  const test = new NameGenerator();
```  
----
[return to :pushpin:](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#now-that-we-know-the-this-object-it-is-important-to-understand-how-it-is-bind-to-the-scope-pushpin)
# **THIS** Implicit binding           
**THIS** inside a method gets it's value from the inmediate object.

----
```  
  function Reed(){
    return this.prop;
  }
  let op = {
    prop:37
  }
  op.Reed=Reed;
  op.B = {
    prop:31,
    Reed
  }
  console.log(op.B.Reed())
```  
----
**THIS** inside de Prototype chain (Inheritence)
In the following example, the "Object p" doesn't possess the "method f", when "method f" is call the search starts at "Object p" and after no
find it the search goes to its "Prototype o". If the "method f" is found in the "object o" chain prototype then **THIS**'s value 
it will be "Object p" as is the method belongs to the "Object p".

----
```
  var o={
    f:function(){return this.a+ this.b}
  }
  var p=Object.create(o)
  p.a=4;
  p.b=1;
  console.log(p.f())// 5
```  
----
**THIS** in "GETTER" or "SETTER"
A function use as a "GETTER" or "SETTER" has its **THIS** value bind to the "Object" from which the property was stablish.

----
```
  function modulus(){
    return Math.sqt(this.re*this.re + this.im*this.im)
  }
  let O={
    re:1,
    im:-1,
    get phase(){
      return Math.atan2(this.im.this.re)
    }
  }
  Object.defineProperty(O, 'modulus',{get: modulus, enumerable:true, configurable:true})
  console.log(O.phase, O.modulus)// -0.7853981633974483 1.4142135623730951
  /* Note
    GET and SET are defined as funcstions but they are use as properties.
  */
```  
----
In this case we see the method invocation pattern. The **THIS** keyword is really useful when use for object and is good practice to set the strict mode
on functions which have it in their context.

----
```
  const fn = function() {
    return this;
  };
  const obj1 = { fn }; 
  const obj2 = { fn }; 
  obj1.fn() === obj1; // true
  obj1.fn() === obj2; // false
  obj2.fn() === obj1; // false
  obj2.fn() === obj2; // true
  const foo = function(){
    'use strict'
    console.log(this === window)
  }
  let user = {
    count: 10,
    foo,
    foo1: function(){
      console.log(this === window)
    }
  }
  user.foo()// false
  const fun1 = user.foo1; 
  fun1()// true
  user.foo1()// false
```  
----
Function receives an object as an argument and adds a method

----
```
  const tellName = function(obj){
    obj.talk = function(){
      console.log(this.name)
    }
  }
  let matt = {
    name:"Matt",
    Age:22
  }
  let john = {
    name:"John",
    age:25
  }
  tellName(matt)
  tellName(john)
  matt.talk()// Matt
  john.talk()// John
```  
---
Function assigns a method after definition

----
```
  const tellLastName = function(){
    return this.father_lastname +' '+ this.mother_lastname;
  }
  let noel = {
    name:'Carlos Noel',
    father_lastname:'Soto',
    mother_lastname:'Calderon'
  }
  noel.lastnames = tellLastName;
  noel.lastnames()// Soto Calderon
```  
----
Function returning object with **THIS** in a method

----
```
  const Person = function(name,age,mother){
    return{
      name:name,
      age:age,
      talk: function(){
        console.log(this.name)
      },
      mother: {
        name:mother,
        talk: function(){
          console.log(this.name)
        }
      }
    }
  };
  let ana = Person('ana',25,'claire')
  ana.talk()// ana
  ana.mother.talk()// claire
```  
----

When using **THIS** inside a method is referring to the object which the method belongs. **THIS** object can call others methods and properties
which the objects owns.

----
```
  const cleanTable = function(){
    console.log(`cleaning ${this.table}`)
  }
  this.table='window table'
  this.garage={
    table:'garage table',
    cleanTable
  }
  let johnsRoom={
    table:'johns Table',
    cleanTable
  }
  johnsRoom.cleanTable()// cleaning johns Table
  this.garage.cleanTable()// cleaning garage table
```  
----

**THIS** inside of classes
When **THIS** is use inside a method's class it refers to the object's instance from which the method was invoke.

----
```
  class createRoom{
    constructor(name){
      this.table = `${name}'s table`
    }
    cleanTable(soap){
      console.log(`cleaning ${this.table} using ${soap}`)
    }
    cleanTable= function(soap){
      console.log(`cleaning ${this.table} using ${soap}`)
    }
  }

  let john = new createRoom('John')
  let jill = new createRoom('jill')
  john.cleanTable('Shampoo')// cleaning John's table using Shampoo
  jill.cleanTable('Soap')// cleaning jill's table using Soap
```  
----
[return to :pushpin:](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#now-that-we-know-the-this-object-it-is-important-to-understand-how-it-is-bind-to-the-scope-pushpin)
# **THIS** Explicit binding
Functions in JavaScript are a special type of Object (Function.Prototype) and as all Objects it has methods: **call**, **apply** and **bind**. These functions set the
function context in other words change the function owner and there for the **THIS** value. 

Call function
In this method the arguments are pass individually and the first parameter define a reference to **THIS**

----
```
  const talk = function(l1, l2, l3){
    console.log(`My name is ${this.name} and I speak ${l1}, ${l2} and ${l3}`)
  }
  let barbie = {
    name: 'Barbie',
    edad: 27
  }
  let languages=['English','Spanish','French']
  talk.call(barbie, languages[0], language[1], language[2])
  const Person = function(fn, ln){
    this.first_name = fn;
    this.last_name = ln;
    this.displayName = function(){
      console.log(`Name: ${this.first_name} ${this.last_name}`)
    }
  }
  let person = new Person("John", "Reed");
  let person2 = new Person("Paul", "Adams");
  person.displayName()// Name: John Reed
  person2.displayName()// Name: Paul Adams
  person2.displayName.call(person);// Name: John Reed
  function hello(thing){
    console.log(`${this} says hello ${thing}`)
  }
  hello.call("Yehuda", "world")// Yehuda says hello world
  var person1 = {
    name: 'Angelina',
    lastName: 'Jolie',
    birtDay: '04/06/1975',
    lastMovie: 'The Tourist'
  }
  var person2 = {
    name: 'Scarlett',
    lastName: 'Johansson',
    birthDay: '22/11/1984',
    lastMovie: 'We bought a Zoo'
  }
  var printName = function(){
    console.log(`${this.name} ${this.lastName}`)
  }
  printName.call(person1)// Angelina Jolie
  printName.call(person2)// Scarlett Johanson
  printName.bind(person2)()// Scarlett Johanson
  printName.bind(person1)()// Angelina Jolie
```  
----
Example 2

----
```
  'use strict'
  this.table='window table'
  this.garage={
    table:'garage table'
  }
  let johnsgarage={
    table:'Johns Table'
  }
  const cleanTable=function(soap){
    console.log(`cleaning ${this.table} using ${soap}`)
  }
  cleanTable.call(this,'some soap')
  cleanTable.call(this.garage.'some soap')
  cleanTable.call(johnsgarage.'some soap')  
```  
----
Apply function
In this method the first method let us defined the deference to **THIS**, and second parameter is an array passing the function's arguments.
(Dispatcher pattern)

----
```
  let person1 = {
    name: 'Angelina',
    lastName: 'Jolie',
    birthDay: '04/06/1975',
    lastMovie: 'The Tourist'
  }
  let person2 = {
    name: 'Scarlett',
    lastName: 'Johanson',
    birthDay: '22/11/1984',
    lastMovie: 'We bought a Zoo'
  }
  const printName = function(){
    console.log(`${this.name} ${this.lastName}'s last movie was ${this.lastMovie}`)
  }
  const updatePerson = function(name, lastName, birthDay, lastMovie){
    this.name = name;
    this.lastName = lastName;
    this.birthDay = birthDay;
    this.lastMovie = lastMovie;
  }
  const dispatch = function(object, method, args){
    method.apply(object, args)
  }
  dispatch(person1, printName);// 
  dispatch(person2, updatePerson, ['Scarlett','Johanson','22/11/1982','The Avengers'])// 
  dispatch(person2, printName);//
```  
----
Bind function
All functions can call this function since in JavaScript function are consider a special type of "Object".
In this method the first parameter is for defining the reference to **THIS**, all other arguments are pass individually. **BIND** generates
a new function fixing **THIS** value as is was in the **lexical context**.

----
```
  function Person(fn, ln){
    this.first_name = fn;
    this.last_name = ls;
    this.displayName = function(){
      console.log(`Name: ${this.first_name} ${this.last_name}`)
    }
  }
  let person = new Person("John", "Reed")
  let person2 = new Person("Paul", "Adams")
  let personDisplay = person.displayName.bind(person2)
  person.displayName()// Name: John Reed
  person2.displayName()// Name: Paul Adams
  personDisplay()// Name: Paul Adams
```  
----
Building our own Bind function

----
```
  const person={
    text:"Greeting to all",
    hello(){
      console.log(this.text)
    }
  }
  var myBinding = function(func, thisValue){
    return function(){
      return func.apply(thisValue, arguments)
    }
  }
  var newBinding = myBinding(person.hello, person)
  newBinding()//Greeting to all
```  
----
[return to :pushpin:](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#now-that-we-know-the-this-object-it-is-important-to-understand-how-it-is-bind-to-the-scope-pushpin)
# References
https://medium.com/better-programming/understanding-the-this-keyword-in-javascript-cb76d4c7c5e8
https://medium.com/@osmanakar_65575/javascript-lexical-and-dynamic-scoping-72c17e4476dd
https://spin.atomicobject.com/2014/10/20/javascript-scope-closures/
https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Operadores/this
https://www.geeksforgeeks.org/javascript-this-identifier/
https://masteringjs.io/tutorials/fundamentals/this
https://yeisondaza.com/entendiendo-this-javascript
https://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/
http://adripofjavascript.com/blog/drips/dealing-with-the-dangers-of-this-in-constructors.html
https://medium.com/better-programming/understanding-the-this-keyword-in-javascript-cb76d4c7c5e8
https://www.etnassoft.com/2012/01/12/el-valor-de-this-en-javascript-como-manejarlo-correctamente/
https://www.youtube.com/watch?v=NV9sHLX-jZU&t=506s
https://www.youtube.com/watch?v=Pv9flm-80vM&t=66s

[return :point_up:]()
