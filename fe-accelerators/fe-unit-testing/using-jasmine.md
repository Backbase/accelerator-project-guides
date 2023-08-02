# Using Jasmine
## Introduction

*   1 [Introduction](#Introduction)
    *   1.1 [Tools](#Tools)
    *   1.2 [Readable](#Readable)
        *   1.2.1 [3xA](#3xA)
*   2 [Implementation](#Implementation)
    *   2.1 [Testing components](#Testing-components)
        *   2.1.1 [Standard (simple) cases](#Standard-(simple)-cases)
        *   2.1.2 [Testing with marbles](#Testing-with-marbles)
    *   2.2 [Testing services](#Testing-services)
    *   2.3 [Testing pipes](#Testing-pipes)
    *   2.4 [Testing directives](#Testing-directives)
*   3 [Tips](#Tips)
    *   3.1 [Test range manipulation](#Test-range-manipulation)

### Tools

Default tests framework used in Angular for unit, and integration tests is Jasmine.

Using Angular CLI for components, pipes and etc. we get, ready to run, unit tests.

Below article aims to provide instruction on how to write unit tests, for most common cases, to keep consistent style and supplement Angular unit testing [documentation](https://angular.io/guide/testing-components-basics "https://angular.io/guide/testing-components-basics").

Code snippets used in article comes from example app available [here](https://stash.backbase.com/projects/CSFRON/repos/jasmine-unit-testing "https://stash.backbase.com/projects/CSFRON/repos/jasmine-unit-testing").

### Readable

#### 3xA

The AAA (Arrange-Act-Assert) pattern has become almost a standard way of writing unit tests for a method under test.

*   The Arrange section of a unit test method initializes objects and sets the value of the data that is passed to the method under test.
    
*   The Act section invokes the method under test with the arranged parameters.
    
*   The Assert section verifies that the action of the method under test behaves as expected.
    

This pattern allows you to keep a descriptive way of your test. Test written this way, become good code documentation and clarifying business logic.

In Jasmine there are two basic methods allowing to keep mentioned style: "**describe**" which provides context (arrange step), and allow to share tests in sections.

So in the arrange section you only have code required to setup that specific test.

Second method is “**it**”, which is a pronoun for the test target. It defines the single spec, which should contain one or more expectations that tests the state of the code. Here we implement the assert step.

> Connecting the function name "**it**" and the argument "**description**" as a complete sentence we create a scenario of the test.

The Act step should be well planned to maximise readability and stay with DRY rule.

Depending on the context, we can make some actions in the "it" section (in simple single cases), or we can use the "beforeEach" method (placed in the description section) which will execute some portion of code before each run test from the section.

But even in simple tests, if there are no barriers, we can use the “beforeEach” method to increase readability of code, and also make clear "act step" separation.

**Example below:**

```
describe('TransactionItemComponent', () => {
  let component: TransactionItemComponent;
  
  beforeEach(() => {
    component = new TransactionItemComponent();
  });
  
  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

In further steps we will also show more complicated cases.

At this introduction level it's also worth to mention about one more tip increasing readability.

It's partially connected with the DRY rule, but maybe not so obvious in writing unit tests.

> Some repeatable actions can be closed in functions. Eg, taking some view elements in few places of code we can have a function:

```
function getList() {
  return fixture.debugElement.queryAll(By.css('bb-okr-transaction-item'));
}
```

This way, reading tests we better know what kind of elements are taken.

## Implementation

### Testing components

Components are the base of Angular apps, so we should start from testing these elements. Angular [documentation](https://angular.io/guide/testing-components-basics "https://angular.io/guide/testing-components-basics") and CLI schematics suggest one way of component testing with [TestBed](https://angular.io/api/core/testing/TestBed "https://angular.io/api/core/testing/TestBed") class.

> But following Joe Eames tip in case when we don’t have correlation with component view required to be tested, and we have simple component without many dependencies. We can omit TestBed and test component as a standard class. It can simplify your test and increase the speed.

#### Standard (simple) cases

One example has been already presented above, another case with one dependency is below:

```
describe('TransferFormComponent', () => {

 let component: TransferFormComponent;
 const amount = 20;

 beforeEach(() => {
   component = new TransferFormComponent(fakeTransactionService as TransactionsService);
 });

 it('should create', () => {
   expect(component).toBeTruthy();
 });

 it('should have selected merchant', () => {
   expect(component.form.get('merchant')?.value).toEqual(MerchantsMock[0]);
 });

 it('should have have invalid form', () => {
   expect(component.form.invalid).toBeTruthy();
 });
 ...
});
```

Transaction service stub is placed in separate file because is used in two components test, and looks as follow:

```
export const fakeTransactionService: Pick<TransactionsService, 'addTransaction' | 'getTransactionList'> = {
  addTransaction(amount: number, merchant: Merchant): void {},
  getTransactionList(): Observable<Array<Transaction>> {
    return of(TransactionsMock)
  }
};
```

Test for _TransactionGeneratorComponent_ is an example how to handle component where time case matters.

The component depends on TransactionService, so will use the same fakeTransactionService stub as above. In current app example components listen events from child components, and when all of them will emit value to their parent, then with 100ms delay new transaction with generated transaction will be added. We don’t tests observables, because all properties are private (not tested), but we can handle their execution spying service.

To spy if serviced will call the method for adding transactions we have to add spy before test.

```
beforeEach(() => {
  spyOn(fakeTransactionService, 'addTransaction');
  clock = jasmine.clock().install();
});
```

We also use **Jasmine’s Clock class** to simulate time delay. It is worth mentioning that using jasmine clock it’s important to uninstall it after tests. Useful for this case is “afterEach” method

```
afterEach(() => {
  clock.uninstall();
});
```

Then we can check, that if nothing emitted, service won’t be called

```
describe('when subcomponents have not emitted events', () => {
  it('should not add transfer', () => {
    clock.tick(100);
    expect(fakeTransactionService.addTransaction).not.toHaveBeenCalled();
  });
});
```

And when all 3 subcomponents emit their values, after the same time service will be called.

As we don't test the service here but use a stub service, the same way we don't test sub-components, which are covered on their own (**it’s isolated, not integration test**). That's why we call public components methods in “act” step.

```
describe('when subcomponents emitted events', () => {
  beforeEach(() => {
    component.takeAmount(100);
    component.takeMerchant(MerchantsMock[2]);
    component.takeSign(1);
  });

  it('should not add transfer', () => {
    clock.tick(100);
    expect(fakeTransactionService.addTransaction).toHaveBeenCalledWith(100, MerchantsMock[2]);
  });
});
```

#### Testing with marbles

In the above example we have an example of a **hot observable** (where producers are "outside" the final observable, which triggers service). Now we will see an example of a **cold observable** (producer inside).

For this purpose we will use marble testing. Main reason why this way is useful is readability. Similar as marble diagrams help us understand f.e. [RxJS operators](https://rxmarbles.com "https://rxmarbles.com"), the same marble testing lets us describe complex streams in a short readable way. RxJS supports marble tests, and delivers tools, to handle that. For more information check [here](https://rxjs.dev/guide/testing/marble-testing "https://rxjs.dev/guide/testing/marble-testing").

Component **TransactionContractorsSelectorComponent** as subcomponent of transaction generator is in charge of randomisation merchant of transaction. Each 5, 5 second next merchant from the static list is taken. 

In the test we want to check if in 17 seconds from component initialisation observable emits next merchant from the list. In this test also jasmine clock will be used, but stream result will be checked with [marble syntax](https://rxjs.dev/guide/testing/marble-testing#marble-syntax "https://rxjs.dev/guide/testing/marble-testing#marble-syntax").

To run marble test we use TestScheduler provided by RxJs library.

```
beforeEach(() => {
  scheduler = new TestScheduler((actual, expected) => {
    expect(actual).toEqual(expected);
  });
  spyOn(component.resultEmitter, 'emit');

  clock = jasmine.clock().install();
  component.ngOnInit();
  clock.tick(wholeCycleTime);
});
```

In this place we also install a jasmine clock and simulate 17 seconds delay. Since we used ReplaySubject in our class, all emitted states in this time are remembered, and to check if result is correct we use expected marble and state as follow:

```
const expectedMarble = '(abc)';
const expectStates = {a: MerchantsMock[0], b: MerchantsMock[1], c: MerchantsMock[2]};
```

And finally, test:

```
it('should generate full merchant list in sequence', () => {
  scheduler.run(({expectObservable}) => {
    expectObservable(component.currentState).toBe(expectedMarble, expectStates);
  });
});
```

As a second example we have got a simple component making pseudo randomization of numbers. Each number position is taken from a different array iterated in an infinite loop.

The list values are as follows:

```
export const amountSequences = {
  units: [2,4,6,1,2,8,9,5],
  tens: [5,3,7,4,5,9,6,0,1,2,4],
  hundreds: [2,6,1,0,9,3]
}
```

Increasing index in each array. We should get numbers as: 252, 634 etc.

To avoid timeout connection in test, in this case we test part of the infinite stream.

```
subStream = component.amount.pipe(take(10));
```

To prevent hardcoding values in test (in case when values in array would change), there is a function prepared in test to grab next generated values based on amountSequences: **takeNumberByIndex**.

In this case arrange step looks similar to the previous one, but we have more complex expectations, which clearly proves how useful marble testing can be.

Are test, looks as follows:

```
describe('taking first 10 values', () => {
  beforeEach(() => {
    subStream = component.amount.pipe(take(10));
    expectedMarble = '3000ms a 2999ms b 2999ms c 2999ms d 2999ms e 2999ms f 2999ms g 2999ms h 2999ms i 2999ms (j|)';
    expectedNumbers = {
      a: takeNumberByIndex(1),
      b: takeNumberByIndex(2),
      c: takeNumberByIndex(3),
      d: takeNumberByIndex(4),
      e: takeNumberByIndex(5),
      f: takeNumberByIndex(6),
      g: takeNumberByIndex(7),
      h: takeNumberByIndex(8),
      i: takeNumberByIndex(9),
      j: takeNumberByIndex(10),
    };
  });
  
  it('should emit new amounts every 3 seconds', () => {
    scheduler.run(({expectObservable}) => {
      expectObservable(subStream).toBe(expectedMarble, expectedNumbers);
    });
  });
});
```

We expect to get next amount each tree seconds. Worth to take a look at time values in marble syntax, where just first step we have 3000ms then 2999ms. Explanation for that we will find [here](https://rxjs.dev/guide/testing/marble-testing#time-progression-syntax "https://rxjs.dev/guide/testing/marble-testing#time-progression-syntax") in NOTE.

### Testing services

Testing services is pretty much the same as component testing.

In example-app we have just one service, responsible for all cases connected with transactions. In test we will cover getting transaction list method, and http request.

To test transaction list we use "subscribe and assert" pattern.

```
describe('calling transaction list', () => {
  it('should return transaction list', (doneFn) => {
    service.getTransactionList().subscribe(list => {
      expect(list.length).toBe(TransactionsMock.length);
      doneFn();
    });
    afterHttpCall();
  });
});
```

It's important in this approach, to remember about calling done function. In case of missing this step we can have green tests even though in reality they are failing. To cover http request we will use delivered by Angular [HttpTestingController](https://angular.io/api/common/http/testing/HttpTestingController "https://angular.io/api/common/http/testing/HttpTestingController")

In test there is a function, where we connect testing http request (if appropriate method has been called), and mocking response. To use it we have to import to test HttpClientTestingModule, which includes mentioned testing controller.

```
describe('TransactionsService', () => {
 let service: TransactionsService;
 let httpController: HttpTestingController;
 const newTransactionAmount = 999.99;

 const afterHttpCall = () => {
   httpController.expectOne({ method: 'GET', url: '/api/transactions' }).flush(TransactionsMock);
 }

 beforeEach(() => {
   TestBed.configureTestingModule({
     imports: [HttpClientTestingModule],
     providers: [TransactionsService],
   });
   service = TestBed.inject(TransactionsService);
   httpController = TestBed.inject(HttpTestingController);
 });
	…
});
```

### Testing pipes

Despite the fact that pipes are strictly connected with template logic, we have to remember that on the other hand, there are still standard classes implementing PipeTransform interfejs, which require transform method to be implemented. 

So the simplest way of testing them is again more or less the same as above. We just have to test if the transform method returns expected values, depending on given values.

In example-app there are two pipes presenting two ways of possible usage.

Formatting strings is probably the most common way of pipes usage.

One of the transaction generator subcomponents depends on transaction sign (which depends if the transaction became income or expense). Component emits a sequence of multipliers (1/-1) to their parent and we want to see a reflection of the current state.

That's why the template uses a pipe, which display +/- sign depends on the multiplier.

Test for this pipe is simple

```
describe('TransactionSignPipe', () => {
  let pipe: TransactionSignPipe;
  
  beforeEach(() => pipe = new TransactionSignPipe());
  
  it('create an instance', () => {
    expect(pipe).toBeTruthy();
  });
  
  describe('when given value less or equal zero', () => {
    it('should return minus sign', () => {
      expect(pipe.transform(-1)).toEqual('-');
    });
  
    it('should return minus sign', () => {
      expect(pipe.transform(0)).toEqual('-');
    });
  
    it('should return minus sign', () => {
      expect(pipe.transform(null)).toEqual('-');
    });
  });
  
  describe('when given value higher than zero', () => {
    it('should return plus sign', () => {
      expect(pipe.transform(1)).toEqual('+');
    });
  });
});
```

Another case of pipes usage can array filtering, used in example-app at transaction list. Example test can look like this:

```
describe('when looking for merchant with "b"', () => {
  beforeEach(() => {
    filteredList = pipe.transform(TransactionsMock, 'b');
  });
  
  it('should return two values', () => {
    expect((filteredList as Array<Transaction>).length).toEqual(2);
  });
});
```

### Testing directives

In some cases like f.e. testing custom form controls implementing ControlValueAccessor, where user interface get some values, and component prepares some final single form control value more powerful than classic simple isolated tests can be test which we can be seen some how as integration tests..

When we want to test such form control behavior in the context of a data set through the user interface, we can use the way with HostTestComponent (TestHostComponent).

In file with the test we create a component with logic allowing us to test all required scenarios, and in the template of this component we put out components or directives we want to test.  
  

In our example app there is a directive which (like directive have it in nature) makes some manipulation on view layer level. That's why we will use this approach, to test the directive's functionality.

The role of this directive is to mark the text which is used to filter transactions. It's strictly connected with transaction item component, that's why we use this component in test host component.

```
@Component({
  selector: 'bb-host-test-component',
  template: `
    <bb-okr-transaction-item [transaction]="transaction" bbOkrFilterHighlighter [filter]="query">
    </bb-okr-transaction-item>
  `
})
class HostTestComponent {
  get transaction(): Transaction {
    return TransactionsMock[1];
  }
  query = '';
}
```

To do this we have to declare also this component besides directive.

```
beforeEach(() => {
  TestBed.configureTestingModule({
    declarations: [HostTestComponent, FilterHighlighterDirective, TransactionItemComponent],
  }).compileComponents();
});
```

We also simplify understanding of what are expecting to find in component’s view, using to this separate function:

```
function getHighlightedText(): string {
  const elem = fixture.debugElement.query(By.css('strong'));
  if (!elem) {
    return ''
  }
  return elem.nativeElement.innerHTML;
}
```

In test we make sure that nothing is highlighted when filter phrase is empty

```
describe('after initialization', () => {
  it('should not highlight anything', () => {
    fixture.detectChanges();
    expect(getHighlightedText()).toEqual('');
  });
});
```

and exactly the same value is selected as passed to filter

```
const highlightTxt = TransactionsMock[1].merchant.name.substr(1,3);
...

describe('when filter query set', () => {
  beforeEach(() => {
    component.query = highlightTxt;
    fixture.detectChanges();
  });

  it('should highlight looking text', () => {
    expect(getHighlightedText()).toEqual(highlightTxt);
  });
});
```

## Tips

### Test range manipulation

Writing test we usually focus on small area of tests. To avoid running all tests present in app jasmine provide us few helpful methods like: "**fdescribe**", "**xdescribe**", "**xit**" and [more](https://jasmine.github.io/api/4.0/global "https://jasmine.github.io/api/4.0/global").

“x” works more or less like “comment” If we use xit or xdescribe we skip single test or whole section.

But on the other hand when we want focus just on particular section and skip everything else, it's enough to add "f" to chosen section and run just those one.