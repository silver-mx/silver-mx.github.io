---
layout: post
title: Dissecting an Angular Component Test
date: 2021-12-25 10:00
category: Angular
author: silver-mx
tags: [angular testing, jasmine]
summary: This article explains the basic structure that composes a unit test for an Angular component. 
---

<!-- TOC -->

- [The component under testing](#the-component-under-testing)
  - [Basics of an Angular unit test](#basics-of-an-angular-unit-test)
    - [Grouping all specs (line 4-6)](#grouping-all-specs-line-4-6)
    - [Compiling the component (lines 8-13)](#compiling-the-component-lines-8-13)
    - [Instantiating a component instance (lines 15-19)](#instantiating-a-component-instance-lines-15-19)
    - [lines 21-23](#lines-21-23)

<!-- /TOC -->

Starting with automated tests can be daunting as it requires understanding all elements needed to get a simple test working. Angular testing is not the exception, and that is why this article describes how to get started writing components tests.

---

***NOTE***: See the code in [Github](https://github.com/silver-mx/angular-testing-basics/tree/main/src/app/shipping).

---

<br/>

## The component under testing

The component includes a simplistic logic to handle the shipping of online purchases. The *shipping component* lets you decide whether the shipment will be picked up at a store, or delivered to the customer's home address. 

![component screenshot](/assets/images/AngularTestingBasics-component.png)

<br/>

### Basics of an Angular unit test

Angular's CLI automatically creates empty test files for newly created components (`ng g component shipping`). The *empty test* file for our shipping component is:

---
***NOTE:*** Check the [Jasmine docs](https://jasmine.github.io/tutorials/your_first_suite) for detail about the keywords (`describe`, `beforeEach`, `it`). 

---

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ShippingComponent } from './shipping.component';

describe('ShippingComponent', () => {
  let component: ShippingComponent;
  let fixture: ComponentFixture<ShippingComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ ShippingComponent ]
    })
    .compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(ShippingComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});

```

From the code above, we can highlight the following elements:

<br/>

#### Grouping all specs (line 4-6)

{% highlight typescript %}

describe('ShippingComponent', () => {
  let component: ShippingComponent;
  let fixture: ComponentFixture<ShippingComponent>;

{% endhighlight %}

The `describe` block will group all the specs(tests). Note that the string `ShippingComponent` will join the description given to the spec from line *21*. So our spec will read as: `ShippingComponent should create`.

---
 ***Hint***: Name your specs wisely so that they can serve as documentation.

---

The lines below `describe('ShippingComponent', () => {` show the declarations of variables and constants used throughout the specs. 
  
<br/>

#### Compiling the component (lines 8-13)

{% highlight typescript %}

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ ShippingComponent ]
    })
    .compileComponents();
  });

{% endhighlight %}

`beforeEach` runs initialization that applies to all specs. The logic makes use of `TestBed` to configure the environment to unit test our component (see [TestBed docs](https://angular.io/api/core/testing/TestBed)). 

`TestBed.configureTestingModule({...})` creates a testing module to test our component. Angular applications are modular and contain at least one module(see [NgModules](https://angular.io/guide/architecture-modules)). Modules serve as containers to include components, service providers, and other code. A testing module normally receives the following metadata:
  * `declarations`: Defines the components, directives, or pipes belonging to the testing module, `ShippingComponent` in our example. 
  * `imports`: Lists other modules whose exported classes are needed by the tested component's template.
  * `providers`: Defines creators of the services needed for our testing modules. `TestBed` will inject such services into the tested component via dependency injection.

---
***NOTE:*** The `imports` and `providers` sections will be added in the second part of this article when we set the `ShippingComponent`'s dependencies.

---

`TestBed.compileComponents()`: Compiles all the components declared in the test module. It is an asynchronous operation, and that is why the function passed to `beforeEach` is marked as `async`. We do not need to call this method when using `ng test` from the CLI, as the CLI compiles the application before running the tests (see [Angular docs](https://angular.io/guide/testing-components-scenarios#calling-compilecomponents)), but other scenarios may require it.

<br/>

#### Instantiating a component instance (lines 15-19)
jkhkjhkj


 * `component`:  to hold an instance of the component under testing and 
 * `fixture`: is a test harness for interacting with the created component and its corresponding element.


#### lines 21-23
