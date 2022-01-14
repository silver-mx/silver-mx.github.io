---
layout: post
title: Angular Component Testing 1 - Basic Structure
date: 2021-12-25 10:00
category: Angular
author: silver-mx
tags: [angular testing, component testing, jasmine]
summary: This article explains the basic structure that composes a unit test for an Angular component. 
---

Recently I started writing automated tests in Angular, and something that bothered me was that I understood parts but not the test as a whole. This article gives a walkthrough of the skeleton of an empty component test as it includes important concepts that support more complex tests. Hope it helps :slightly_smiling_face: 

---
:point_right: Check [Part 2 - Unit testing of Angular components](/angular/2022/01/04/dissecting-angular-component-testing-part-2.html) to see how the component is unit tested with help of `TestBed` and `ComponentFixture`.

---

<!-- TOC -->

- [The component under testing](#the-component-under-testing)
  - [Grouping all specs (line 4-6)](#grouping-all-specs-line-4-6)
  - [Compiling the component (lines 8-13)](#compiling-the-component-lines-8-13)
  - [Instantiating a component (lines 15-19)](#instantiating-a-component-lines-15-19)
  - [Verifying the component instance (lines 21-23)](#verifying-the-component-instance-lines-21-23)
- [Running our test](#running-our-test)
- [Summary](#summary)

<!-- /TOC -->

---
:bangbang: Find the code for this article in [Github](https://github.com/silver-mx/angular-testing-basics/tree/dissecting_angular_component_test_part_1/src/app/shipping).

---
<br/>
## The component under testing

We use a *simplistic* component that handles the shipping of online purchases. The *shipping component* allows the user to decide whether the shipment is picked up at a store or delivered to the customer's home address. 
<br/>

Angular's CLI `ng g component shipping` automatically creates the skeleton of a component and its unit test. This initial unit test looks as:


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

See below an explanation of the relevant parts:

---
:exclamation: Check the [Jasmine docs](https://jasmine.github.io/tutorials/your_first_suite) for detail about the keywords (*i.e.*, `describe`, `beforeEach`, `it`), in case you are not familiar with them. 

---
<br/>
### Grouping all specs (line 4-6)

{% highlight typescript %}

describe('ShippingComponent', () => {
  let component: ShippingComponent;
  let fixture: ComponentFixture<ShippingComponent>;

{% endhighlight %}

<br/>

The `describe` block groups all the specs(tests). Note that the string `ShippingComponent` joins the description given to the spec from line *21*. So our spec reads as: `ShippingComponent should create`.

---
 :bulb: Name your specs wisely, as well written test can serve as documentation.

---

The lines below `describe('ShippingComponent', () => {` show the declarations of variables and constants used throughout the specs. 
  
<br/>

### Compiling the component (lines 8-13)

{% highlight typescript %}

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ ShippingComponent ]
    })
    .compileComponents();
  });

{% endhighlight %}

`beforeEach` runs initialization that applies to all specs. The logic makes use of `TestBed`, a testing utility provided by Angular for constructing dynamically a test module that emulates an `@NgModule`. `TestBed` helps us to configure the environment to unit test a component, and to inject services and other dependencies required by the tested component (see [TestBed docs](https://angular.io/guide/testing-services#angular-testbed)).

`TestBed.configureTestingModule({...})` creates a testing module to test our component. Angular applications are modular and contain at least one module(see [NgModules](https://angular.io/guide/architecture-modules)). Modules serve as containers to include components, service providers, among others. A testing module normally receives the following metadata:
  * `declarations`: Defines the components, directives, and pipes belonging to the testing module defined under `configureTestingModule()`. Normally you include the component under testing, `ShippingComponent` in our example, but other classes can be included if needed.
  <br/>

  * `imports`: Lists other modules whose exported classes are needed by the tested component. An example is [Angular Material's](https://material.angular.io/components/categories)'s modules, which are imported when the tested component uses UI components like `MatSelect` , `MatInput`, `MatButton` in its [template](https://angular.io/guide/architecture-components#templates-and-views).
  <br/>

  * `providers`: Lists creators of services not exported by modules listed in `imports`, and that are required by the tested component. `TestBed` injects such services into the tested component via dependency injection. An example is a [*spy*](https://jasmine.github.io/tutorials/your_first_suite#section-Spies) that gets provided/injected into the component for stubbing responses and isolating the component's testing.
  
---
:exclamation: The `imports` and `providers` sections are added in the second part, where we set the `ShippingComponent`'s dependencies.

---

`TestBed.compileComponents()`: Compiles all the components declared in the test module. It is an asynchronous operation, and that is why the function passed to `beforeEach` is marked as `async`. ***Note:*** CLI's `ng test` compiles the application before running the tests (see [Angular docs](https://angular.io/guide/testing-components-scenarios#calling-compilecomponents)), thus calling `compileComponents()` is not required, but other scenarios may need it.

<br/>

### Instantiating a component (lines 15-19)

{% highlight typescript %}

  beforeEach(() => {
    fixture = TestBed.createComponent(ShippingComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

{% endhighlight %}

Once again we find a `beforeEach` block, but this time it receives a synchronous function. 
<br/>

`TestBed.createComponent(ShippingComponent)` is used to create a `fixture`. `fixture` is an instance of [`ComponentFixture`](https://angular.io/guide/testing-components-basics#componentfixture), which is a test harness for interacting with the created component and its [template](https://angular.io/guide/architecture-components#templates-and-views). `fixture.componentInstance` gets an instance of `ShippingComponent`.

Further, `ComponentFixture` has [`DebugElement`](https://angular.io/guide/testing-components-basics#debugelement), which helps us to access and interact with the HTML elements declared in the component's template.

Finally, [`fixture.detectChanges()`](https://angular.io/guide/testing-components-scenarios#detectchanges) tells the `TestBed` to perform data binding. [Data binding](https://angular.io/guide/binding-syntax#binding-syntax) means keeping the page up-to-date according to the application's state. ***Note:*** normally, Angular performs change detection automatically when running an application. However,  when testing with `TestBed`, change detection needs to be triggered manually to allow the developer to perform checks or assertions about the application's state before the binding occurs.

<br/>
### Verifying the component instance (lines 21-23)

{% highlight typescript %}
  it('should create', () => {
    expect(component).toBeTruthy();
  });
{% endhighlight %}

The spec verifies that `fixture.componentInstance` succeeded when creating a component instance. It is a  handy spec because it signals when the `TestBed` is unable to provide all required dependencies to the component.

---
:bulb: If your spec fails, check that the metadata provided to `TestBed.configureTestingModule({...})` (*i.e.,* `imports` and `providers`) satisfies the dependencies that your component's implementation declares.

---

<br/>
## Running our test

It is time to see the results of our test. The test should run without errors considering that we have not modified the component yet. So running:

 `ng test --include=src/app/shipping/shipping.component.spec.ts`

 tells Angular's CLI to compile the component and execute [Karma](https://karma-runner.github.io/latest/index.html), Angular's test runner. Short after, Karma opens a new browser window with the test results:

 ![test results](/assets/images/first-spec-pass.png)

<br/>
## Summary

 This article described basic concepts common to Angular component tests:

 * The initial test skeleton created by `ng g component shipping` includes two `beforeEach` blocks; one asynchronous and one synchronous. The asynchronous one configures the `TestBed` and compiles the component. The synchronous one creates a `fixture` to instantiate the component.
 <br/>
 
 * The initial spec `should create` verifies that `TestBed` is correctly configured for instantiating the tested component. The spec fails when `TestBed` does not include metadata to set up the component's dependencies (*i.e.*, services, components, directives).

:+1: __Thanks for reading!!!__

<br/>

---
:point_right: Check [Part 2 - Unit testing of Angular components](/angular/2022/01/04/dissecting-angular-component-testing-part-2.html) to see how [Angular Material's Component Harness](https://material.angular.io/guide/using-component-harnesses) is used to unit test the shipping component.

---



