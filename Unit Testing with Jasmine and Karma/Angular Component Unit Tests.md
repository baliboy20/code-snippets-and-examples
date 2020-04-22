#### Testing a component

```$xslt
describe('AppComponent', () => {
  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [
        AppComponent
      ],
    }).compileComponents();
  }));

  it('should create the app', () => {
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.debugElement.componentInstance;
    expect(app).toBeTruthy();
  });

  it(`should have as title 't01'`, () => {
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.debugElement.componentInstance;
    expect(app.title).toEqual('t01');
  });

  it('should render title', () => {
    const fixture = TestBed.createComponent(AppComponent);
    fixture.detectChanges();
    const compiled = fixture.debugElement.nativeElement;
    expect(compiled.querySelector('.content span').textContent).toContain('t01 app is running!');
  });
});
```

 #### Memorise 
* describe
* beforeEach
* async
* it
* fixture  testBed createComponennt
* fixture debug componentInstance
* fixture detectChanges
* fixture debugElement nativeElement
* nativeElement.querySelector


into a bedroom 
test the bed and config module. compile

(make the bed) bed create components
(try the bed) fixture.debugElement.componentInstance
(try for comfort) fixture.detectchanges
(natives bed) fixture.debugElement.nativeElement
(ask questions) node.querySelector().textContent

