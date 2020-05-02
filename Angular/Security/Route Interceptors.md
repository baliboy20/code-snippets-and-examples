##Route Inteceptors - Basic
#####Route config
```typescript
const routes: Routes = [
  {
    path: 'field-collection',
    component: FieldCollectionComponent,
    canActivate: [RouteGuardService]
  },
];
```
####Interceptor Class
```typescript

@Injectable({
  providedIn: 'root'
})
export class RouteGuardService implements CanActivate {

  constructor(private router: Router, private session: UserSession) { }

  isLoggedIn = false;

  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot)
    : Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {
    if (!this.session) {
      throw new Error('Session not found');
      return false;
    }
    if (this.session.isLoggedIn) {
      return true;
    } else {
      console.log('RouteGard - intended route', ( state.url) );
      this.router.navigate(['logon'],  { queryParams: { returnUrl: state.url }});
      return false;
    }
  }
}
```
p.s. Can also use a provider with a validation function

#### Session Class
```typescript

@Injectable({
  providedIn: 'root'
})
export class UserSession {
  isLoggedIn = false;

  setLoggedIn() {
    this.isLoggedIn = true;
  }

  setLoggedOut() {
    this.isLoggedIn = false;
  }
}

```

#### Logon Component
```typescript
export class LogonComponent implements OnInit {

  constructor(private router: Router,
              private activeRoute: ActivatedRoute,
              private session: UserSession) {
  }
  ngOnInit() {}

  _isUrl(value: string) {
    if (!value) {
      throwError(`Invalid route ${value}`);
    } else {
      return value;
    }
  }

  /**
   * function scaffold
   *  authentication passed ok to re-route
   */
  onOk() {
    this.session.isLoggedIn = true;
    this.activeRoute.queryParams
      .pipe(map(params => params.returnUrl), map(this._isUrl))
      .subscribe(url => {
        console.log('params===', url);
        this.router.navigate([url]);
      },
        (error) => {
        console.log(error);
        this.router.navigate(['']);
        }
      ).unsubscribe();
  }

  onCancel() {
    this.session.isLoggedIn = false;
    this.router.navigate(['field-collection']);
  }
}
```

