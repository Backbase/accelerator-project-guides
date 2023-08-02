# Using Jest
# About

Standard Angular framework for writing unit tests is Jasmine, but that doesn’t mean it’s the only one that can be used. This document is about to provide basic knowledge about writing unit tests in Angular using Jest. Beside highlighting some differences (Jest is quite similar to Jasmine overall), the document should also provide general concept strategies of testing different Angular pieces.

Below you will find a list of the topics covered by this document:

*   1 [Prerequisites](#Prerequisites)
*   2 [Arrange - Act - Assert model](#Arrange---Act---Assert-model)
*   3 [Testing components](#Testing-components)
    *   3.1 [Isolated tests](#Isolated-tests)
    *   3.2 [Component inputs/outputs](#Component-inputs/outputs)
    *   3.3 [Testing templates](#Testing-templates)
*   4 [Testing observables using marbles](#Testing-observables-using-marbles)
*   5 [Testing services](#Testing-services)
    *   5.1 [Testing HttpClient](#Testing-HttpClient)
*   6 [Testing pipes](#Testing-pipes)
*   7 [Testing directives](#Testing-directives)

# Prerequisites

Before we start, there are some links worth checking out. Good place to start is of course Angular documentation about [Testing](https://angular.io/guide/testing-attribute-directives "https://angular.io/guide/testing-attribute-directives"). Most of the topics will be covered in this tutorial using Jest as a test framework, but some more in-depth examples can be found on Angular pages.

This document does not cover how to set up a project to use Jest. Instructions on how to do this can be found in [Cookbook: Set up Jest testing](https://backbase.atlassian.net/wiki/spaces/ES/pages/3513614522/Cookbook+Set+up+Jest+testing "https://backbase.atlassian.net/wiki/spaces/ES/pages/3513614522/Cookbook+Set+up+Jest+testing").

Finally, examples used in this document are provided as an example app that can be found on [stash](https://stash.backbase.com/projects/CSFRON/repos/jest-unit-testing/browse "https://stash.backbase.com/projects/CSFRON/repos/jest-unit-testing/browse").

# Arrange - Act - Assert model

While writing unit tests we should try to follow the Arrange - Act - Assert model, which means, whenever it’s possible, unit tests should consist of three separate blocks.

*   Arrange - this is where we set prerequisites of the test eg. setting a spy on a method that we want to test
    
*   Act - this is where we’re firing our method or do other action that we want to test
    
*   Assert - in this final block we check if the result of our tests are correct
    

```
it('should emit value from form input', () => {
  const expectedValue = 'Test';
  const filterChangeSpy = jest.spyOn(component.filterChange, 'emit');

  component.onFilterChange(expectedValue);

  expect(filterChangeSpy).toHaveBeenCalledWith(expectedValue);
});
```

In the example above we can clearly distinguish these three blocks. First two lines are our Arrange block, where we define expected outcome and set a spy on component output. Next, we’re calling the _onFilterChange_ method as a part of our Act block. At the end, we’re checking if our spy was called with expected value - this is our Assert block.

At this point it’s also worth mentioning that the Assert should not contain multiple, unrelated assertions. It’s better to write multiple tests, each one testing different units of code, than one big test that checks everything.

# Testing components

Testing components is the most common thing in Angular and in many cases, the most complex one. There are at least two approaches that we can take. We can test both - component class and component template - this is the standard approach that we learn from official Angular documentation. We can also perform more isolated tests, were we ignore the HTML template, and focus only on our logic inside the component class.  
  

Testing class only has some benefits. Because we don't need to worry about the template, we also don’t have to worry about template dependencies like child components or pipes. We also don’t have to prepare a _TestBed_, which should result in faster tests execution, and we have full control over the lifecycle of our component.  
  

There are of course examples of components that have logic in the template itself that may be worth testing, but if that’s the case, maybe the component itself is too complex and worth splitting on smaller ones. Later on we will take a look at testing templates, but let's start with isolated tests.

## Isolated tests

In this example, we will look at the main component of our example journey - _TransactionViewComponent_ - which is also the only smart component that we have. The propose of it is fairly simple: it should pass data from the _TransactionsService_ and into dumb components. It should also pass new transfers of money from dumb component into _TransactionsService._

```
export class TransactionsViewComponent {
  showFilter = this.transactionsService.config.showFilter;
  transactions$ = this.transactionsService.transactions$;
  balance$ = this.transactionsService.balance$;

  constructor(private readonly transactionsService: TransactionsService) {}

  transfer(transfer: Transfer): void {
    this.transactionsService.transfer(transfer);
  }
}
```

Our component have one, very simple method that we can test and also have one dependency. There is also no logic in HTML that would be worth testing, so we can omit it entirely to focus only on component class. 

To test this component we need to do tree things:

*   Create a stub for _TransactionsService_ that our component requires
    
*   Create the component itself providing the stub instead of real service (we don’t test the service in here)
    
*   Test our method
    

```
describe('TransactionsViewComponent', () => {
  const transactionServiceStub: Pick<TransactionsService, 'transactions$' | 'transfer' | 'config'> = {
    transactions$: of([]),
    transfer: jest.fn(),
    config: {
      showFilter: true,
    },
  };

  let component: TransactionsViewComponent;

  beforeEach(() => {
    component = new TransactionsViewComponent(transactionServiceStub as TransactionsService);
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
```

To create our stub we use _TransactionService_ but picking only methods and attributes that our component class requires. Because we’re passing transaction list without modifying it, we can just provide an empty array. We could create a mock for the transfer function for ourselves, but in this case, it’s better to just use Jest's built-in mechanism to create that mock, so we can spy on it later on.

Now, this is the biggest change in comparison to the standard method of scaffolding unit tests by Angular CLI. In the _beforeEach_ function, instead of configuring _TestBed_ and providing all the dependencies into it, we can just create an instance of our component class.

One thing to note in here are lifecycle hooks. Because we don’t have _TestBed_, we also don’t have access to the _detectChange_ function. Instead, we can just trigger lifecycle hooks manually, for example in our _beforeEach_ method we could have code like this:

```
beforeEach(() => {
  component = new TransactionsViewComponent(transactionServiceStub as TransactionsService);
  component.ngOnInit();
});
```

We don’t trigger the _ngOnInit_ method, because we didn’t implement it.  
  

Let’s take a look at how we test our transfer method. 

```
  describe('making transfer', () => {
    it('should forward transfer into TransactionService', () => {
      const expected = TransferMock;
      component.transfer(expected);

      expect(transactionServiceStub.transfer).toHaveBeenCalledWith(expected);
    });
  });
```

  
In the Arrange section of our test we just set the expected result. We could omit this section and just use _TransferMock_ directly. Next, in Act block, we call the transfer method with our mocked data. Finally in the Assert method we can check if _TransferMock_ was passed into _TransactionService_. We can spy on the transfer method, because we used Jest to create a mock for this method.

```
transfer: jest.fn(),
```

  
On the other hand, if we would create a mock by ourselves eg.

```
transfer: (transfer: Transfer) => {},
```

then, we would have to create a spy for this method inside our component, so it would look like this:

```
describe('making transfer', () => {
  it('should forward transfer into TransactionService', () => {
    const expected = TransferMock;
    const transferSpy = jest.spyOn(component, 'transfer')

    component.transfer(expected);

    expect(transferSpy).toHaveBeenCalledWith(expected);
  });
});
```

One last thing that requires explanation is the _TransferMock_ object. It’s an object containing mocked data and stored inside _mocks/_ directory.

```
import { Transfer } from '../model/transfer';

export const TransferMock: Transfer = {
  name: 'Test',
  amount: 100,
};
```

## Component inputs/outputs

Our application may consists a bunch of small, dumb components, that have inputs and sometimes outputs. We can use _TransferFormComponent_ as an example of how we can test these.

```
export class TransferFormComponent {
  @Input() balance: number | null = 0;
  @Output() transfer = new EventEmitter<Transfer>();

  transferForm = new FormGroup({
    merchant: new FormControl(null, Validators.required),
    amount: new FormControl(null, [Validators.required, (control: AbstractControl) => this.balanceValidator(control)]),
  });
```

Our component have one input, for taking current balance and one output for emitting a new transfer into our _TransferViewComponent_. There is also a very simple form. In this case we’re not using form builder, to not create extra dependency, but that doesn’t you should avoid using it. We also have one custom validator:

```
 private balanceValidator(control: AbstractControl): ValidationErrors | null {
  const currentBalance = this.balance ?? 0;
  const value = control.value ?? 0;

  return currentBalance - value < 0 ? { balance: true } : null;
}
```

Balance validator checks if we have sufficient funds for making a transfer. Knowing that we can check if our component outputs the correct value. First, we can set balance input of our component:

```
beforeEach(() => {
  component = new TransferFormComponent();
  component.balance = 200;
});
```

and then, we can provide valid data into our form:

```
it('should emit form value', () => {
  const expectedValue = { name: 'Test', amount: 100 };
  const transferSpy = jest.spyOn(component.transfer, 'emit');

  component.transferForm.setValue({ merchant: 'Test', amount: 100 });
  component.makeTransfer();

  expect(transferSpy).toHaveBeenCalledWith(expectedValue);
});
```

In the third line we create a spy for our output and then in the Assert block we check if it was called with expected value. We can also change component inputs inside our tests to perform different tests, eg. we can check if our _balanceValidator_ works if we don’t provide any balance:

```
it('balance validator should work when no balance provided', () => {
  const control = component.transferForm.controls['amount'];
  component.balance = null;

  control.setValue(1);
  control.markAsTouched();

  expect(component.isControlInvalid('amount')).toBe(true);
  expect(component.hasError('amount', 'balance')).toBe(true);
});
```

Both _isControlInvalid_ and _hasError_ are helper methods defined inside our component.

## Testing templates

In most cases, testing component templates is not necessary, as it’s a good practice to not put business logic inside HTML templates. But there may be a case where testing a component template brings some value. To do this, we have to scaffold our test in a more traditional way, using _TestBed_.

```
beforeEach(() => {
  TestBed.configureTestingModule({
    declarations: [TransactionsListComponent],
    schemas: [NO_ERRORS_SCHEMA],
  });

  fixture = TestBed.createComponent(TransactionsListComponent);
  component = fixture.componentInstance;
  component.transactions$ = of(TransactionsMock);
});
```

Unfortunately the template of our component depends on some other components which we’re not testing right now. Missing dependencies would result in errors. To fix it, we add _NO\_ERROR\_SHEMA_ into our _TestBed_ configuration. Alternative to that would be to create stub for all child components and use them in a declarations array.

For this example we will use _TransactionsListComponent_, which has just two inputs and renders other components depending on the value of these inputs.

```
export class TransactionsListComponent implements OnInit {
  @Input() showFilter = true;
  @Input() transactions$: Observable<Transaction[]> = of([]);
```

Here is a template of this component:

```
<div class="bb-block--lg" *ngIf="showFilter">
  <bb-okr-filter (filterChange)="filter($event)"></bb-okr-filter>
</div>

<ng-container *ngIf="filteredTransactions$ | async as transactions">
  <bb-okr-transaction-item *ngFor="let transaction of transactions" [transaction]="transaction">
  </bb-okr-transaction-item>
</ng-container>
```

Now, the first thing we can test here is if _showFilter_ works correctly. By default, _showFilter_ is true.

```
it('should render filter by default', () => {
  fixture.detectChanges();
  const filter = fixture.debugElement.query(By.css('bb-okr-filter'));

  expect(filter).toBeTruthy();
});
```

After calling _detectChanges_ we have the template rendered and we can check if our filter element exists in the DOM tree. 

Now, let’s test the case were _showFilter_ was set to false:

```
it('should hide filter', () => {
  component.showFilter = false;

  fixture.detectChanges();
  const filter = fixture.debugElement.query(By.css('bb-okr-filter'));
  
  expect(filter).toBeFalsy();
});
```

Last thing that may be worth it is if the output of our filter component triggers the correct method in our _TransactionsListComponent_.

```
 it('should call filter method on filterChange', () => {
  const filterSpy = jest.spyOn(component, 'filter');

  fixture.detectChanges();
  const filter = fixture.debugElement.query(By.css('bb-okr-filter'));
  filter.triggerEventHandler('filterChange', 'Test');

  expect(filterSpy).toHaveBeenCalledWith('Test');
});
```

First we create a spy for our filter method and get our filter element. We simulate output of filter element by manually triggering event. The output inside _FilterComponent_ looks like this:

```
@Output() filterChange = new EventEmitter<string>();
```

Remember: we are not checking integration between _FilterComponent_ and _TransactionsListComponent_. _FilterComponent_ can change. Here we’re only testing if specific template event triggers correct component method.  
  

For transactions list we can check two things - if correct number of transaction items are rendered:

```
it('should render correct number of transaction rows', () => {
  fixture.detectChanges();
  const rows = fixture.debugElement.queryAll(By.css('bb-okr-transaction-item'));

  expect(rows.length).toEqual(TransactionsMock.length);
});
```

and if correct data is passed into transaction item input:

```
it('should set transaction item input', () => {
  fixture.detectChanges();
  const transactionItem = fixture.debugElement.query(By.css('bb-okr-transaction-item'));

  expect(transactionItem.properties.transaction).toEqual(TransactionsMock[0]);
});
```

Like in example with _FilterComponent_, we’re not doing integration tests here. _TransactionItemComponent_ implementation can change. What we check here is just if our component template is rendered correctly (if properties of child elements are set as expected).

# Testing observables using marbles

Our _TransactionsListComponent_, not only renders the list of transactions provided by _TransactionsViewComponent_ from _TransactionsService_, but also allows us to filter the list based on output provided by _FilterComponent_ and stored in Subject. Then these two streams are combined to return an observable with a list of filtered transactions.

```
export class TransactionsListComponent implements OnInit {
  @Input() showFilter = true;
  @Input() transactions$: Observable<Transaction[]> = of([]);

  filteredTransactions$: Observable<Transaction[]> = of([]);

  private filter$ = new BehaviorSubject<string>('');

  ngOnInit(): void {
    this.filteredTransactions$ = combineLatest([this.transactions$, this.filter$]).pipe(map(this.filterTransactions));
  }

  filter(filter: string): void {
    this.filter$.next(filter);
  }

  private filterTransactions([transactions, filter]: [Transaction[], string]): Transaction[] {
    return transactions.filter((transaction) =>
      transaction.merchant.name.toLocaleLowerCase().includes(filter.toLocaleLowerCase()),
    );
  }
```

Now to test if our filtering method works, we have to check what we get in a stream after providing different values into our filter. To do this, we can use the _TestScheduler_ provided by RxJs library. First, we have to initialize it:

```
describe('filtering transactions', () => {
  let scheduler: TestScheduler;
  
  beforeEach(() => {
    scheduler = new TestScheduler((actual, expected) => {
      expect(actual).toEqual(expected);
    });

    fixture.detectChanges();
  });
  ```

_TestScheduler_ allows us to test asynchronous code (observables in this case) in a synchronous way by virtualizing time lapse. After defining, we can use it in our test suites. First thing we can check using it is if the full list of transactions is returned if no filter was provided.

```
it('should return full transactions list', () => {
  scheduler.run(({ expectObservable }) => {
    const expectedMarble = 'a';
    const expected = { a: TransactionsMock };
    
    expectObservable(component.filteredTransactions$).toBe(expectedMarble, expected);
  });
});
```

In the example above, we’re defining how many different values should be returned by the stream, in our case it will be just one value. We also define what this value should contain, so we’re expecting just a full list of transactions. This is because we set component input to be observable from _TransactionsMock_.

Now it’s time to check if our filtering is working correctly by simulating typing in different values, and checking if the number of transactions is what we expect knowing what’s inside the _TransactionsMock_ object.  
  
```
it('should filter list by merchant name', () => {
  scheduler.run(({ expectObservable, cold }) => {
    const expectedMarble = 'a-b-c-a';
    const expected = { a: 11, b: 1, c: 2 };

    cold('--a-b-c', { a: 'Backbase', b: 'Online', c: '' }).subscribe((value) => component.filter(value));
    
    expectObservable(component.filteredTransactions$.pipe(map((transactions) => transactions.length))).toBe(
      expectedMarble,
      expected,
    );
  });
});
```

First we will check the starting number of transactions, then after some time we will provide a ‘Backbase’ string into our filter and check again the number of transactions. This time we expect the list to contain just one element. After some time we change our filter value to ‘Online’, and our list should contain two elements. At the end we will clear our filter to get a full list again.

In our example an expected stream should have four states, but the first and last should be the same. We also separated our states by some virtual time passage. Now that we have our expected stream defined, it’s time to simulate the user of the application. To do that, we will use another helper provided by _TestScheduler_, that will allow us to create a stream with exact values in exact time spans.

```
cold('--a-b-c', { a: 'Backbase', b: 'Online', c: '' }).subscribe((value) => component.filter(value));
```

At the beginning, our hypothetical user will do nothing and that is represented by ‘-’. This is when we expect a full list of transactions. Then, our user will wait some time, which is represented by another ‘-’ and provide ‘Backbase’ value into our filter. After some time he will type ‘Online’, and again after some more time he will clear the filter. Each value provided by the user will be passed into our filter method to be used by our _filteredTransactions$_ observable.

```
expectObservable(component.filteredTransactions$.pipe(map((transactions) => transactions.length))).toBe(
  expectedMarble,
  expected,
);
```

# Testing services

Testing services don't differentiate too match between isolated component testing, which means in most cases testing observables and methods while stubbing dependencies. In our example we have two observables to be tested - one returning list of transactions and second returning current balance. We also have one method - transfer - that has influence on both observables mentioned. 

```
private internalTransactions$ = new BehaviorSubject<Transaction[]>([]);
transactions$ = combineLatest([this.transactionsHttpService.transactions$, this.internalTransactions$]).pipe(
  map(([externalTransactions, internalTransactions]) => [...externalTransactions, ...internalTransactions]),
  map(this.sortByDate),
);

balance$ = this.transactions$.pipe(map(this.mapTransactionsIntoBalance));

constructor(
  private readonly transactionsHttpService: TransactionsHttpService,
  @Optional() @Inject(TransactionsConfigurationToken) config: Partial<TransactionsConfiguration> | null,
) {
  this.config = { ...DEFAULT_TRANSACTIONS_CONFIG, ...config };
}

transfer(transfer: Transfer): void {
  const transaction = createMockedTransaction(transfer);
  this.internalTransactions$.next([...this.internalTransactions$.value, transaction]);
}
```

Before we start, we have to create a stub for _TransactionHttpService_ which is used to load transactions from external sources.

```
const TransactionsHttpStub: Pick<TransactionsHttpService, 'transactions$'> = {
  transactions$: of(TransactionsMock),
};
```

For testing observables we will use marbles. For more details about it please look at Testing observables using marbles. First we create our _TestScheduler_:

```
beforeEach(() => {
  service = new TransactionsService(TransactionsHttpStub as TransactionsHttpService, DEFAULT_TRANSACTIONS_CONFIG);
  scheduler = new TestScheduler((actual, expected) => {
    expect(actual).toEqual(expected);
  });
});
```

After that we can use it in our test to check transactions$ and transfer method.

```
it('should add transfer to transactions list', () => {
  scheduler.run(({ expectObservable, cold }) => {
    const expectedMarble = 'ab';
    const expected = {
      a: SortedTransactionsMock,
      b: [createMockedTransaction(TransferMock), ...SortedTransactionsMock],
    };

    cold('-a').subscribe(() => service.transfer(TransferMock));

    expectObservable(service.transactions$).toBe(expectedMarble, expected);
  });
});
```

At the beginning our observable should return just the list provided by external service. Then we add a new transfer to check if the new value of the observable will contain this new transaction. Unit test for balance looks almost the same, but instead of checking the list of transactions, we check the current balance.

## Testing HttpClient

There is a special case that is quite common, which is testing services which contain _HttpClient_ and requests to external endpoints.

There are two common approaches to that. First is to treat _HttpClient_ as any other dependency and just stub it’s _get_, _post_ and other methods that our service depends on. In our example however, we will use another solution which is using a built-in helper in the form of _HttpTestingController_. In our case we have just one observable that returns data from an endpoint using _httpClient_.

```
export class TransactionsHttpService {
  transactions$ = this.httpClient.get<Transaction[]>('/api/transactions');

  constructor(private readonly httpClient: HttpClient) {}
}
```

To use _HttpTestingController_ we have to configure _TestBed_ providing _HttpClientTestingModule_ as an import.

```
describe('TransactionsHttpService', () => {
  let service: TransactionsHttpService;
  let httpController: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [TransactionsHttpService]
    });

    service = TestBed.inject(TransactionsHttpService);
    httpController = TestBed.inject(HttpTestingController);
  });
```

Then, to test our observable we can use the Subscribe & Assert pattern. It’s another way to test observables. In most scenarios it is much better to use marbles for testing streams, but marbles weren’t really meant to work with _HttpTestingController_. One thing to remember when using Subscribe & Assert is to not forget to call _done_ function. If you don’t want to mix Subscribe & Assert with marbles, then It’s probably a better idea to stub _HttpClient_.

```
it('should return transactions list', (done) => {
  service.transactions$.subscribe((data) => {
    expect(data).toEqual(TransactionsMock);
    done();
  });

  const req = httpController.expectOne({ method: 'GET', url: '/api/transactions' });
  req.flush(TransactionsMock);
});
```

Once we subscribe to our observable, we can then use _HttpController_ to check if the correct endpoint with the correct method was called, and finally return mocked data. If we would like to test if there was no request, then instead of _expectOne_, we would use _expectNone_ function.

After each test we also call the _verify_ function to check if there were no additional requests that we didn’t expect.

```
afterEach(() => {
  httpController.verify();
});
```

# Testing pipes

Testing pipes is a fairly simple thing to do, as we can test it as any other class, but there should be only one public method.  
  
```
export class CustomCurrencyPipe implements PipeTransform {
  transform(value: number, currency: string, creditDebitIndicator?: string): string {
    const amount = creditDebitIndicator === 'DBIT' ? value * -1 : value;
    return `${getCurrencySymbol(currency, 'narrow')} ${amount.toFixed(2)}`;
  }
}
```

In our situation we have two cases to test. First is when we have just _value_ and _currency_ without _creditDebitIndicator_.

```
it('should correctly format currency', () => {
  expect(pipe.transform(23.222, 'EUR')).toEqual('€ 23.22');
});
```

The second scenario is when our pipe was provided with _creditDebitIndicator_.

```
it('should show DBIT transactions as negative', () => {
  expect(pipe.transform(23.222, 'EUR', 'DBIT')).toEqual('€ -23.22');
});
```

As you can see, we can just test the result returned by the transform method, without the need to use the pipe itself in some template. 

# Testing directives

Testing directives is a bit unusual. Most of the time, directives are used to manipulate DOM elements, so to test if the directive works, we need the DOM itself. To achieve that, we have to create a _HostComponent_ that will use our directive inside its template.

```
@Component({
  template: `<div [bbOkrCatetgoryCode]="'red'"></div>`,
})

class HostComponent {}
```

Once we have our _HostComponent_ defined, we can use it to configure _TestBed_ and get access to the DOM element of our directive.

```
beforeEach(() => {
  fixture = TestBed.configureTestingModule({
    declarations: [HostComponent, CatetgoryCodeDirective],
  }).createComponent(HostComponent);

  fixture.detectChanges();
  element = fixture.debugElement.query(By.directive(CatetgoryCodeDirective));
 });
```

Last thing to do is to test if our directive is working as expected, in our case, if the directive sets the element background color.

```
it('should set background color on host element', () => {
  expect(element.nativeElement.style.backgroundColor).toBe('red');
});
```