# 1. INTRODUCTION

JavaScript is a programming language that uses **lexical scope** with **function scope** to define the variables values,
except for the _THIS_ keyword, which is a variable whos value seems to change strangely and that we cannot control.

The reason to this behavior is that the object _THIS_ has some kind of **dynamic scope** to define it's value, 
that's why on execution time _THIS_ changes to values that we didn't defined in our code.

The changes to _THIS_ values are due to the close relationship between the _THIS_ object and the **execution context** (_enviroment or 
scope in which a line of code is being executed_) in JavaScript. 
During the **execution time** of one code line of a JavaScript program, the execution context changes taking into consideration the owner object 
of the function being call and _THIS_ stores a reference to this owner object.

It's important to understand how the value of _THIS_ changes because sometimes it may seem that has one value but it changes to another.

----
Example 1.1 Shows the value of _THIS_ changing in a inner function inside an object's method
```
  let team = {
    name:"Xolos",
    sayName: function(){
      let text= 'The team name is';
      function innerfunction(){
        console.log(`${text} ${this.name}`);
      }
      innerfunction();
    }
  }
  team.sayName() // The team name is 
```
----
As mention before the execution context changes at execution time due to the function being call by it's owner object.
Since a function can be call diferent ways (implicit, explicit, event listenes, others.), the same function can have diferent execution context, 
whichs means a function can have diferents values for _THIS_ keyword. 

An easy way to know the value of _THIS_ is to determine how the function that contains the _THIS_ keyword was call at execution time. 
It helps to answer the question: Who call the function?

In execution time who can call a function to execute are (posibles values of _THIS_): 
- the **Global object** (window if we are working on a browser, global object on NodeJS).
- objects (a define structure with methods and properties).
- DOM elements.
- primitives data types (string, boolean, number, bigint, undefined, symbol). 

In JavaScript is not posible to alter how the lexical scope works, but it is possible to control the execution context in which we call our functions. 
We can control and fixed the value of _THIS_ with: **clousure**, **call**, **apply**, **bind** and **arrow functions**.

----
Example 1.2 Shows how the _THIS_ value changes to the value we set using the "call" function. Also the **Symbol** data type is use as a property.
```
  function playerGoalRecord(){ 
    return this+1
  }
  function Player(){ 
    return this
  }
  function typeofVersion1(){ 
    return typeof this
  }
  function typeofVersion2(){ 
    return `${typeof this}`
  }
  var velocity = Symbol("km/s")
  let Andrea_Pirlo = new Player();  
  Andrea_Pirlo[velocity]=2.4
  
  console.log(playerGoalRecord.call(1))// 2
  console.log(playerGoalRecord.call("12"))// "121"
  console.log(playerGoalRecord.call(true))// 2
  console.log(playerGoalRecord.call(false))// 1
  console.log(playerGoalRecord.call(0.3))// 1.3
  //console.log(playerGoalRecord.call(velocity))// Uncaught TypeError: Cannot convert a Symbol value to a number
  console.log(Player.call(velocity))// Symbol {Symbol(velocity)}
  console.log(typeof velocity)// "symbol"
  console.log(typeofVersion1.call(velocity))// object
  console.log(typeofVersion2.call(velocity))// "object"
  console.log(Andrea_Pirlo[velocity])// 2.4
```
----
### Now lets understand how the _THIS_ object is bind to the scope: :pushpin:
 - [Default(window)](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#this-default-binding)
 - [new keyword](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#this-new-binding)
 - [Lexical](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#this-lexical-context-binding)
 - [implicit](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#this-implicit-binding)
 - [explicit](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#this-explicit-binding)
               
# 2. _THIS_ Default binding
In the **Global Execution Context**, _THIS_ refers to the **Global Object**(Window), if we set **strict mode** "ON", _THIS_ value is undefined,
however we can still refer to the Global Object via the global property **globalThis**.

----
Example 2.1 Shows _THIS_ keyword as a reference to the "Global Object".
```
  this.league='LigaMX'
  this.USA={
    league:'MLS'
  }
  let England = {
    league:'Premier League'
  }
  console.log(window.league)// LigaMX
  console.log(window.USA.league)// MLS
  console.log(England.league)// Premier League
  console.log(this.England.league)// Uncaught TypeError: Cannot read property 'league' of undefined
```
----
In the Example 2.1 the "Uncaught TypeError" is because the object "England" is a private property since the **LET** keyword was used,
if the **VAR** keyword is use instead then it will possible the access to the property this way.

When using _THIS_ inside a function its value refers to the "Global Object".

----
Example 2.2 Show _THIS_'s value inside a function
```
  function test(){
    console.log(this === window)
  }
  console.log(this === window)// true
  test()// true
``` 
----
By default there is no data encapsulation when _THIS_ is use inside a function, you can change the _THIS_'s value outside the function.

----
Example 2.3 Show how _THIS_'s property value is change outside the function
```
  const NumberTeams = function(){
    console.log(this.size)
  }
  const AddTeam = function(){
    console.log(++this.size)
  }
  NumberTeams()// undefined
  window.size = 22
  NumberTeams()// 22
  AddTeam()//23
```
----
For correcting this behavior the "strict mode" is use. A function which has "use strict" in their scope, changes the value of _THIS_ to "undefined" instead 
of the "window object".

----
Example 2.4 Show the use of "strict mode" inside a function
```
  const NumberTeams = function(){
    'use strict'
    console.log(this.size)
  }
  window.size = 22
  NumberTeams()// Uncaught TypeError: Cannot read property 'size' of undefined
```
----
However even if the "strict mode" is "ON", we get a reference to _THIS_ in the "global scope".

Example 2.5 Shows a way to set the _THIS_ values using the reference "globalThis"
```
  this.competition="World Soccer Cup"
  function Global(){
    'use strict'
    var that=globalThis
    that.competition="America Soccer Cup"
    return that;
  }
  console.log(Global().competition)// "America Soccer Cup"
  console.log(this.competition)// "America Soccer Cup"
```
----
[return to :pushpin:](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#now-that-we-know-the-this-object-it-is-important-to-understand-how-it-is-bind-to-the-scope-pushpin)
# 3. _THIS_ new binding
The **NEW** keyword is use together with **construction functions** (a function use to create object) assigning it to a variable to create objects. In which _THIS_ value
refers to the new created instance.

----
Example 3.1 Shows how two diferent objects using the "new" keyword and a "construction funcion"
```
  const Team = function(name, league){
    this.name = name;
    this.league = league;
    this.info = function(){
      console.log(`Team: ${this.name}, League: ${this.league}`)
    }
  }
  let chelsea = new Team("Chelsea","Premier League")
  let milan = new Team("AC Milan", "Serie A")
  chelsea.info()// Team: Chelsea, League: Premier League
  milan.info()// Team: AC Milan, League: Serie A
  chelsea===milan// false
```
----

----
Example 3.2 Shows the creation of an object using the prototype chain
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

----
Example 3.3 Shows how a construction function can modify the object using the _return_ keyword
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
Example 3.4 Shows how the construction function modify its behavior when the _new_ keyword is missing
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
Example 3.5 Shows a better handling construction function when the _new_ keyword is missing
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
# 4. _THIS_ Lexical Context binding

----
Example 4.1
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
In regular functions every new function defined has its own _THIS_ keyword value, which owner is the global object (window), except if the
function uses "strict mode" in which case is set to "undefined". The _THIS_ keyword is not very useful in functions, where they really shine
is when is use with objects.

----
Example 4.2
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
Using _THIS_ in a function with 'strict mode'

----
Example 4.3
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
Using _THIS_ in an inner function
Each function possess its own relation with _THIS_ even inner functions. And by default _THIS_'s value is the "Global Object".
In the next example, we bind the _THIS_ value with the **CALL** function but then its change to "undefined" (because the "strict mode" is "ON")
in the inner function. This is an unwanted behavior. (When a function changes the _THIS_ value is know as a function loosing its context).

----
Example 4.4
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

A) _THIS_ with closures is not a very good solution

----
Example 4.5
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
Example 4.6
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
Unlike regular functions, **arrow functions** don't have their own _THIS_ keyword, it uses the **OUTTER SCOPE**'s _THIS_ value. In the next
example the "innefunction" takes the "outterfunction"'s _THIS_ which was set with the **CALL** function.

----
Example 4.7
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

----
Example 4.8
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
the _THIS_ keyword, due to the method being added to the class. This means that _THIS_ is referring to the class. One way to change 
_THIS_ value is via 'Event Listener'.

----
Example 4.9
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
In the example above when we press the "button" it show us the "Cannot read ..." message because the "button" modifies the _THIS_ value.
In the class's constructor lexical scope _THIS_ refers to the class an that's why it was able to find the "addName" method, but when the 
button is press _THIS_ value changes because the one "who call the function" was the button not the object instance of the class 
'NameGenerator'. And the button doesn't has the properties "name" and "counter". We can correct this error using the **BIND** function.

----
Example 4.10
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
In the next example use arrow function to keep the lexical context of _THIS_.

----
Example 4.11
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
# 5. _THIS_ Implicit binding           
_THIS_ inside a method gets it's value from the inmediate object.

----
Example 5.1
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
_THIS_ inside de Prototype chain (Inheritence)
In the following example, the "Object p" doesn't possess the "method f", when "method f" is call the search starts at "Object p" and after no
find it the search goes to its "Prototype o". If the "method f" is found in the "object o" chain prototype then _THIS_'s value 
it will be "Object p" as is the method belongs to the "Object p".

----
Example 5.2
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
_THIS_ in "GETTER" or "SETTER"
A function use as a "GETTER" or "SETTER" has its _THIS_ value bind to the "Object" from which the property was stablish.

----
Example 5.3
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
In this case we see the method invocation pattern. The _THIS_ keyword is really useful when use for object and is good practice to set the strict mode
on functions which have it in their context.

----
Example 5.4
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
Example 5.5
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
Example 5.6
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
Function returning object with _THIS_ in a method

----
Example 5.7
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

When using _THIS_ inside a method is referring to the object which the method belongs. _THIS_ object can call others methods and properties
which the objects owns.

----
Example 5.8
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

_THIS_ inside of classes
When _THIS_ is use inside a method's class it refers to the object's instance from which the method was invoke.

----
Example 5.9
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
# 6. _THIS_ Explicit binding
Functions in JavaScript are a special type of Object (Function.Prototype) and as all Objects it has methods: **call**, **apply** and **bind**. These functions set the
function context in other words change the function owner and there for the _THIS_ value. 

Call function
In this method the arguments are pass individually and the first parameter define a reference to _THIS_

----
Example 6.1
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

----
Example 6.2
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
In this method the first method let us defined the deference to _THIS_, and second parameter is an array passing the function's arguments.
(Dispatcher pattern)

----
Example 6.3
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
In this method the first parameter is for defining the reference to _THIS_, all other arguments are pass individually. **BIND** generates
a new function fixing _THIS_ value as is was in the **lexical context**.

----
Example 6.4
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
Example 6.5
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
# 7. References
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

[return :point_up:](https://github.com/cnsoto/JavaScript-Notes/blob/290822020/THIS%20keyword.md#introduction)
