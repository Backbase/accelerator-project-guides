# Using Spectator
**Presentation of Spectator**

Angular Unit Testing w/ Spectator has been presented in CS Frontend Meeting dated 24.03.2023. Kindly find recording below:

[https://drive.google.com/file/d/1lJ5EZUx0\_4D5f0OywKAJahVhwmHETYkn/view?usp=sharing](https://drive.google.com/file/d/1lJ5EZUx0_4D5f0OywKAJahVhwmHETYkn/view?usp=sharing)

> **Glossary**
> 
> **Jasmine:** An open-source testing framework for JavaScript
> 
> **Jest:** An open-source testing framework for JavaScript

# What is Spectator?

Spectator is a powerful tool to simplify Angular testing. It helps to get rid off all the boilerplate Angular CLI brings with TestBed, leaving you with readable, sleek and streamlined tests.

# What kind of tests can be written with Spectator?

## **Isolated unit tests**

Corresponds to [**Component class testing**](https://angular.io/guide/testing-components-basics#component-class-testing "https://angular.io/guide/testing-components-basics#component-class-testing") mentioned in Angular.io.

Testing Component, Directive, Pipe, Service classes can be considered in this testing category.

Additionally, Spectator supports writing tests for; routing and HTTP communication.

As the name suggests, we always should mock our dependencies since the aim is to write tests in isolation.

## **Shallow unit tests**

Corresponds to [**Component DOM testing**](https://angular.io/guide/testing-components-basics#component-dom-testing "https://angular.io/guide/testing-components-basics#component-dom-testing") mentioned in Angular.io.

With shallow unit testing, we test a component with a template, but we ignore the rendering of child components. By passing `shallow: true` option into configuration, we can achieve this quite easily with Spectator.

## **Integration tests**

Corresponds to [**Component DOM testing**](https://angular.io/guide/testing-components-basics#component-dom-testing "https://angular.io/guide/testing-components-basics#component-dom-testing") mentioned in Angular.io.

With integration testing, we test how 2 or more components work with each other. We can do this when components are depending on each other.

# Why should I consider using Spectator?

Spectator mainly improves DX(Developer Experience) in terms of unit and integration testing. We engineers tend to skip unit testing because testing can be harder and more time consuming than implementation sometimes. Even configuring TestBed correctly may take more time than writing tests. For those reasons, Spectator encourages engineers to write tests.

**Some of the key benefits of Spectator are:**

1.  Reduces boilerplate significantly. Unifies TestBed, ComponentFixture and DebugElement APIs in one consistent, powerful and user-friendly interface – the `Spectator` object
    
2.  Provides an expressive, high-level language for writing Angular tests
    
3.  Easy to adapt incrementally without affecting existing test setup.
    
4.  Flexibility of integrating with Jasmine or Jest.
    
5.  Rich and concise [documentation](https://github.com/ngneat/spectator "https://github.com/ngneat/spectator").
    

# Is there any downsides of using Spectator?

Steep learning curve might be considered as a downside.

# How to install it?

### NPM

`npm install @ngneat/spectator --save-dev`

### Yarn

`yarn add @ngneat/spectator --dev`

>Spectator uses **Jasmine** by default but it has **Jest** support as well.
>
>To be able to use it with Jest, just make sure to target your imports from `@ngneat/spectator/jest` instead of `@ngneat/spectator`.

# Testing Components

> This part is taken from [https://ngneat.github.io/spectator/docs/testing-components/](https://ngneat.github.io/spectator/docs/testing-components/). Please check official documentation for more broad information.

Create a component factory by using the `createComponentFactory()` function, passing the component class that you want to test. The `createComponentFactory()` returns a function that will create a fresh component in each `it` block:

```
import { Spectator, createComponentFactory } from '@ngneat/spectator';
import { ButtonComponent } from './button.component';

describe('ButtonComponent', () => {
  let spectator: Spectator<ButtonComponent>;
  const createComponent = createComponentFactory(ButtonComponent);

  beforeEach(() => spectator = createComponent());

  it('should have a success class by default', () => {
    expect(spectator.query('button')).toHaveClass('success');
  });

  it('should set the class name according to the [className] input', () => {
    spectator.setInput('className', 'danger');
    expect(spectator.query('button')).toHaveClass('danger');
    expect(spectator.query('button')).not.toHaveClass('success');
  });
});
```

The `createComponentFactory` function can optionally take the following options which extends the basic Angular Testing Module options:

```
const createComponent = createComponentFactory({
  component: ButtonComponent,
  imports: [],
  providers: [],
  declarations: [],
  entryComponents: [],
  componentProviders: [], // Override the component's providers
  componentViewProviders: [], // Override the component's view providers
  overrideModules: [], // Override modules
  mocks: [], // Providers that will automatically be mocked
  componentMocks: [], // Component providers that will automatically be mocked
  componentViewProvidersMocks: [], // Component view providers that will be automatically mocked
  detectChanges: false, // Defaults to true
  declareComponent: false, // Defaults to true
  disableAnimations: false, // Defaults to true
  shallow: true, // Defaults to false
});
```

The `createComponent()` function optionally takes the following options:

```
it('should...', () => {
  spectator = createComponent({
    // The component inputs
    props: {
      title: 'Click'
    },
    // Override the component's providers
    providers: [],
    // Whether to run change detection (defaults to true)
    detectChanges: false
  });

  expect(spectator.query('button')).toHaveText('Click');
});
```

The `createComponent()` method returns an instance of `Spectator` which exposes the following properties:

*   `fixture` - The tested component's fixture
    
*   `component` - The tested component's instance
    
*   `element` - The tested component's native element
    
*   `debugElement` - The tested fixture's debug element
    

And the following methods:

## inject()

Provides a wrapper for Ivy's `TestBed.inject()`:

```
const service = spectator.inject(QueryService);

const fromComponentInjector = true;
const service = spectator.inject(QueryService, fromComponentInjector);
```

## detectChanges()

Runs `detectChanges` on the tested element/host:

```
spectator.detectChanges();
```

## setInput()

Changes the value of an `@Input()` of the tested component:

```
it('should...', () => {
  spectator.setInput('className', 'danger');

  spectator.setInput({
    className: 'danger'
  });
});
```

## output()

Returns an observable `@Output()` of the tested component:

```
it('should emit the $event on click', () => {
  let output;
  spectator.output('click').subscribe(result => (output = result));

  spectator.component.onClick({ type: 'click' });
  expect(output).toEqual({ type: 'click' });
});
```

## tick(millis?: number)

Run the fakeAsync `tick()` function and call `detectChanges()`:

```
it('should work with tick', fakeAsync(() => {
  spectator = createComponent(ZippyComponent);
  spectator.component.update();
  expect(spectator.component.updatedAsync).toBeFalsy();
  spectator.tick(6000);
  expect(spectator.component.updatedAsync).not.toBeFalsy();
}))
```

# Testing Services

> This part is taken from [https://ngneat.github.io/spectator/docs/testing-services](https://ngneat.github.io/spectator/docs/testing-services). Please check official documentation for more broad information.

The following example shows how to test a service with Spectator:

```
import { createServiceFactory, SpectatorService } from '@ngneat/spectator';

import { AuthService } from 'auth.service.ts';

describe('AuthService', () => {
  let spectator: SpectatorService<AuthService>;
  const createService = createServiceFactory(AuthService);

  beforeEach(() => spectator = createService());

  it('should not be logged in', () => {
    expect(spectator.service.isLoggedIn()).toBeFalsy();
  });
});
```

The `createService()` function returns `SpectatorService` with the following properties:

*   `service` - Get an instance of the service
    
*   `inject()` - A proxy for Angular `TestBed.inject()`
    

# Testing Directives

> This part is taken from [https://ngneat.github.io/spectator/docs/testing-directives](https://ngneat.github.io/spectator/docs/testing-directives). Please check official documentation for more broad information.

There is a special test factory for testing directives. Let's say we have the following directive:

```
@Directive({ selector: '[highlight]' })
export class HighlightDirective {

  @HostBinding('style.background-color') backgroundColor : string;

  @HostListener('mouseover')
  onHover() {
    this.backgroundColor = '#000000';
  }

  @HostListener('mouseout')
  onLeave() {
    this.backgroundColor = '#ffffff';
  }
}
```

Let's see how we can test directives easily with Spectator:

```
describe('HighlightDirective', () => {
  let spectator: SpectatorDirective<HighlightDirective>;
  const createDirective = createDirectiveFactory(HighlightDirective);

  beforeEach(() => {
    spectator = createDirective(`<div highlight>Testing Highlight Directive</div>`);
  });

  it('should change the background color', () => {
    spectator.dispatchMouseEvent(spectator.element, 'mouseover');

    expect(spectator.element).toHaveStyle({
      backgroundColor: 'rgba(0,0,0, 0.1)'
    });

    spectator.dispatchMouseEvent(spectator.element, 'mouseout');
    expect(spectator.element).toHaveStyle({
      backgroundColor: '#fff'
    });
  });

  it('should get the instance', () => {
    const instance = spectator.directive;
    expect(instance).toBeDefined();
  });
});
```

# Testing Pipes

> This part is taken from [https://ngneat.github.io/spectator/docs/testing-pipes](https://ngneat.github.io/spectator/docs/testing-pipes) . Please check official documentation for more broad information.

The following example shows how to test a pipe with Spectator:

```
import { SpectatorPipe, createPipeFactory } from '@ngneat/spectator';

import { StatsService } from './stats.service';
import { SumPipe } from './sum.pipe';

describe('SumPipe', () => {
  let spectator: SpectatorPipe<SumPipe>;
  const createPipe = createPipeFactory(SumPipe);

  it('should sum up the given list of numbers (template)', () => {
    spectator = createPipe(`{{ [1, 2, 3] | sum }}`);
    expect(spectator.element).toHaveText('6');
  });

  it('should sum up the given list of numbers (prop)', () => {
    spectator = createPipe(`{{ prop | sum }}`, {
      hostProps: {
        prop: [1, 2, 3]
      }
    });
    expect(spectator.element).toHaveText('6');
  });

  it('should delegate the summation to the service', () => {
    const sum = () => 42;
    const provider = { provide: StatsService, useValue: { sum } };
    spectator = createPipe(`{{ prop | sum }}`, {
      hostProps: {
        prop: [2, 40]
      },
      providers: [provider]
    });
    expect(spectator.element).toHaveText('42');
  });
});
```

The `createPipe()` function returns `SpectatorPipe` with the following properties:

*   `hostComponent` - Instance of the host component
    
*   `debugElement` - The debug element of the fixture around the host component
    
*   `element` - The native element of the host component
    
*   `detectChanges()` - A proxy for Angular `TestBed.fixture.detectChanges()`
    
*   `inject()` - A proxy for Angular `TestBed.inject()`
    

# Testing With Host

> This part is taken from [https://ngneat.github.io/spectator/docs/testing-with-host](https://ngneat.github.io/spectator/docs/testing-with-host). Please check official documentation for more broad information.

Testing a component with a host component is a more elegant and powerful technique to test your component. It basically gives you the ability to write your tests in the same way that you write your code. Let's see it in action:

```
import { createHostFactory, SpectatorHost } from '@ngneat/spectator';

describe('ZippyComponent', () => {
  let spectator: SpectatorHost<ZippyComponent>;
  const createHost = createHostFactory(ZippyComponent);

  it('should display the title from host property', () => {
    spectator = createHost(`<zippy [title]="title"></zippy>`, {
      hostProps: {
        title: 'Spectator is Awesome'
      }
    });
    expect(spectator.query('.zippy__title')).toHaveText('Spectator is Awesome');
  });

  it('should display the "Close" word if open', () => {
    spectator = createHost(`<zippy title="Zippy title">Zippy content</zippy>`);

    spectator.click('.zippy__title');

    expect(spectator.query('.arrow')).toHaveText('Close');
    expect(spectator.query('.arrow')).not.toHaveText('Open');
  });
});
```

The host method returns an instance of `SpectatorHost` which extends `Spectator` with the following additional API:

*   `hostFixture` - The host's fixture
    
*   `hostComponent` - The host's component instance
    
*   `hostElement` - The host's native element
    
*   `hostDebugElement` - The host's fixture debug element
    
*   `setHostInput` - Changes the value of an `@Input()` of the host component
    
*   `queryHost` - Read more about [querying](https://ngneat.github.io/spectator/docs/queries "https://ngneat.github.io/spectator/docs/queries") in Spectator
    
*   `queryHostAll` - Read more about [querying](https://ngneat.github.io/spectator/docs/queries "https://ngneat.github.io/spectator/docs/queries") in Spectator
    

# Testing With HTTP

> This part is taken from [https://ngneat.github.io/spectator/docs/testing-with-http](https://ngneat.github.io/spectator/docs/testing-with-http). Please check official documentation for more broad information.

Spectator makes testing data services, which use the Angular HTTP module, a lot easier. For example, let's say that you have service with three methods, one performs a `GET`, one a `POST` and one performs concurrent requests:

```
export class TodosDataService {
  constructor(private httpClient: HttpClient) {}

  getTodos() {
    return this.httpClient.get('api/todos');
  }

  postTodo(id: number) {
    return this.httpClient.post('api/todos', { id });
  }

  collectTodos() {
    return merge(
      this.http.get('/api1/todos'),
      this.http.get('/api2/todos')
    );
  }
}
```

The test for the above service should look like:

```
import { createHttpFactory, HttpMethod, SpectatorHttp } from '@ngneat/spectator';
import { TodosDataService } from './todos-data.service';

describe('HttpClient testing', () => {
  let spectator: SpectatorHttp<TodosDataService>;
  const createHttp = createHttpFactory(TodosDataService);

  beforeEach(() => spectator = createHttp());

  it('can test HttpClient.get', () => {
    spectator.service.getTodos().subscribe();
    spectator.expectOne('api/todos', HttpMethod.GET);
  });

  it('can test HttpClient.post', () => {
    spectator.service.postTodo(1).subscribe();

    const req = spectator.expectOne('api/todos', HttpMethod.POST);
    expect(req.request.body['id']).toEqual(1);
  });

  it('can test current http requests', () => {
    spectator.service.getTodos().subscribe();
    const reqs = spectator.expectConcurrent([
        { url: '/api1/todos', method: HttpMethod.GET },
        { URL: '/api2/todos', method: HttpMethod.GET }
    ]);

    spectator.flushAll(reqs, [{}, {}, {}]);
  });
});
```

We need to create an HTTP factory by using the `createHttpFactory()` function, passing the service that you want to test. The `createHttpFactory()` returns a function which can be called to get an instance of SpectatorHttp with the following properties:

*   `controller` - A proxy for Angular `HttpTestingController`
    
*   `httpClient` - A proxy for Angular `HttpClient`
    
*   `service` - The service instance
    
*   `inject()` - A proxy for Angular `TestBed.inject()`
    
*   `expectOne()` - Expect that a single request was made which matches the given URL and it's method, and return its mock request
    

# Testing With Routing

> This part is taken from [https://ngneat.github.io/spectator/docs/testing-with-routing](https://ngneat.github.io/spectator/docs/testing-with-routing). Please check official documentation for more broad information.

For components which use routing, there is a special factory available that extends the default one, and provides a stubbed `ActivatedRoute` so that you can configure additional routing options.

```
describe('ProductDetailsComponent', () => {
  let spectator: SpectatorRouting<ProductDetailsComponent>;
  const createComponent = createRoutingFactory({
    component: ProductDetailsComponent,
    params: { productId: '3' },
    data: { title: 'Some title' }
  });

  beforeEach(() => spectator = createComponent());

  it('should display route data title', () => {
    expect(spectator.query('.title')).toHaveText('Some title');
  });

  it('should react to route changes', () => {
    spectator.setRouteParam('productId', '5');

     // your test here...
  });
});
```

# Events API

> This part is taken from [https://ngneat.github.io/spectator/docs/events](https://ngneat.github.io/spectator/docs/events). Please check official documentation for more broad information.

Each one of the events can accept a `SpectatorElement` which can be one of the following:

```
type SpectatorElement = string | Element | DebugElement | ElementRef | Window | Document;
```

If not provided, the default element will be the host element of the component under test.

*   `click()` - Triggers a click event:
    

```
spectator.click(SpectatorElement); 
spectator.click(byText('Element'));
```

*   `blur()` - Triggers a blur event:
    

```
spectator.blur(SpectatorElement); 
spectator.blur(byText('Element'));
```

*   `focus()` - Triggers a focus event:
    

```
spectator.focus(SpectatorElement);
```

*   `typeInElement()` - Simulating the user typing:
    

```
spectator.typeInElement(value, SpectatorElement);
```

*   `dispatchMouseEvent()` - Triggers a mouse event:
    

```
spectator.dispatchMouseEvent(SpectatorElement, 'mouseout'); 
spectator.dispatchMouseEvent(SpectatorElement, 'mouseout', x, y, event);
```

*   `dispatchKeyboardEvent()` - Triggers a keyboard event:
    

```
spectator.dispatchKeyboardEvent(SpectatorElement, 'keyup', 'Escape'); 
spectator.dispatchKeyboardEvent(SpectatorElement, 'keyup', { key: 'Escape', keyCode: 27 })
```

*   `dispatchTouchEvent()` - Triggers a touch event:
    

```
spectator.dispatchTouchEvent(SpectatorElement, type, x, y);
```

# Custom Matchers

> This part is taken from [https://ngneat.github.io/spectator/docs/custom-matchers](https://ngneat.github.io/spectator/docs/custom-matchers). Please check official documentation for more broad information.

```
expect('.zippy__content').not.toExist();
expect('.zippy__content').toHaveLength(3);
expect('.zippy__content').toHaveId('id');
expect('.zippy__content').toHaveClass('class');
expect('.zippy__content').toHaveClass('class a, class b');
expect('.zippy__content').toHaveClass(['class a', 'class b']);
expect(spectator.query('.zippy')).toHaveAttribute('id', 'zippy');
expect(spectator.query('.zippy')).toHaveAttribute({id: 'zippy'});
expect(spectator.query('.checkbox')).toHaveProperty('checked', true);
expect(spectator.query('.img')).toHaveProperty({src: 'assets/myimg.jpg'});
expect(spectator.query('.img')).toContainProperty({src: 'myimg.jpg'});
expect('.zippy__content').toHaveText('Content');
expect('.zippy__content').toContainText('Content');

// Note this looks for multiple elements with the class and checks the text of each array element against the index of the element found
expect('.zippy__content').toHaveText(['Content A', 'Content B']);
expect('.zippy__content').toContainText(['Content A', 'Content B']);
expect('.zippy__content').toHaveText((text) => text.includes('..'));
expect('.zippy__content').toHaveValue('value');
expect('.zippy__content').toContainValue('value');

// Note this looks for multiple elements with the class and checks the value of each array element against the index of the element found
expect('.zippy__content').toHaveValue(['value a', 'value b']);
expect('.zippy__content').toContainValue(['value a', 'value b']);
expect(spectator.element).toHaveStyle({backgroundColor: 'rgba(0, 0, 0, 0.1)'});
expect('.zippy__content').toHaveData({data: 'role', val: 'admin'});
expect('.checkbox').toBeChecked();
expect('.button').toBeDisabled();
expect('div').toBeEmpty();
expect('div').toBeHidden();
expect('element').toBeSelected();
// Notice that due to restrictions within Jest (not applying actual layout logic in virtual DOM), certain matchers may result in false positives. For example width and height set to 0
expect('element').toBeVisible();
expect('input').toBeFocused();
expect('div').toBeMatchedBy('.js-something');
expect('div').toHaveDescendant('.child');
expect('div').toHaveDescendantWithText({selector: '.child', text: 'text'});
```

# Schematics

> This part is partially taken from [https://ngneat.github.io/spectator/docs/schematics](https://ngneat.github.io/spectator/docs/schematics). Please check official documentation for more broad information.

Generate component, service, and directive with Spectator spec templates with Angular Cli: (when using it as default)

**Component**

*   Default spec: `ng g cs dashrized-name`
    
*   Spec with a host: `ng g cs dashrized-name --withHost=true`
    
*   Spec with a custom host: `ng g cs dashrized-name --withCustomHost=true`
    

**Service:**

*   Default spec: `ng g ss dashrized-name`
    
*   Spec for testing http data service: `ng g ss dashrized-name --isDataService=true`
    

**Directive:**

```
ng g ds dashrized-name
```

## Default Schematics Collection

To use `spectator` as the default collection in your Angular CLI project, add it to your `angular.json`:

```
ng config cli.defaultCollection @ngneat/spectator
```

The `spectator` schematics extend the default `@schematics/angular` collection. If you want to set defaults for schematics such as generating components with scss file, you must change the schematics package name from `@schematics/angular` to `@ngneat/spectator` in `angular.json`:

```
"schematics": { "@ngneat/spectator:spectator-component": { "style": "scss" } }
```

## Classes Generated via NX Console

NX Console would support creation of classes with spectator spec files(e.g. @ngneat/spectator:spectator-component template) out of the box just after the installation of Spectator.

NX Console

![:books:](./1f4da.png)

**References**

[https://github.com/ngneat/spectator](https://github.com/ngneat/spectator)

[https://ngneat.github.io/spectator/docs/testing-components/](https://ngneat.github.io/spectator/docs/testing-components/)

[https://testing-angular.com/testing-components-with-spectator/#testing-components-with-spectator](https://testing-angular.com/testing-components-with-spectator/#testing-components-with-spectator)