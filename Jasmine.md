# Jasmine

* behaviour driven test framework

## describe

* test suite begins with call to global jasmine function `describe` with 2 parameters, **ja string and a function**
* function contains (is) test or *spec*
  * contains one or more expectations / tests 
  * expectation = assertion that is either true or false

## its just functions

* `it` is normal function, so typical JS rules apply

## matcher 

* check if assertion holds. Every matcher can be negated with `.not` keyword
* jasmines matchers:
  * expect(a).toBe(b)
  * expect(a).not.toBe(null)
  * expect(a).toEqual(12)
  * *regex with toMatch()*
  * expect(a.foo).toBeDefined() (or undefined)
  * expect(a).toBe(null)
  * expect(a).toBeTruthy() (or falsy)
  * expect(array).toContain("bar")
  * expect(e).toBeLessThan(12) (or greater than)
  * expect(a.doBadStuff()).toThrowError("Bad stuff happened")  (or TypeError etc) 

## Grouping related specs with `describe`

*  The string of specs (it block) and describe block will be concatenated
* goal of naming - specs **read as full sentences**


## Setup and Teardown

* `beforeEach`, `afterEach`, `beforeAll` and `afterAll`
* means before each **describe block** (not it block)
* beforeAll is called before all specs in describe are run

## the `this` keyword

* way to share data between beforeEach and it and afterEach 

## nested describe

* is possible

## disable specs and suites

* done with `xdescribe` and `xit`

## Spies

*  *A spy can stub any function and tracks calls to it and all arguments. A spy only exists in the describe or it block it is defined, and **will be removed after each spec**.*
* special matchers `haveBeenCalled` and `haveBeenCalledWith(args)`
* 