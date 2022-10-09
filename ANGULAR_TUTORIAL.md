# Angular

## Prerequisites:

- Configure git and make sure you can clone a repo

### Backend:

- Make sure Java (JDK 17 in this example) is installed

  - https://www.oracle.com/java/technologies/downloads/#java17

- Clone sample spring app
  git clone https://github.com/menuka-s/spring-demo-tour-of-heroes.git

### Frontend:

- Install Node Version Manager
  - https://github.com/nvm-sh/nvm#installing-and-updating
- Install Node >= 14
  - `nvm install 16`
- Install node > 7
  - `npm i npm -g`
- Install Angular CLI globally
  - `npm i @angular/cli -g`

## Tutorial

Check out the [Angular Tour of Heroes tutorial](https://angular.io/tutorial) after this, as it is has much more to go over

- Note: I advise that you use reactive forms instead of the template-driven forms that the tutorial uses

### 1. Create new application

`ng new ng-tour-of-heroes-lite`

- When asked to include routing, say yes
- When asked about styling, choose SCSS instead of CSS

### 2. Create new component

`ng generate component hero`

### 3. Create Hero model

hero.interface.ts:

```
export interface Hero {
  id: number;
  name: string;
}
```

### 4. Create data service

`ng g s hero-service`

```
// Since the example app is using HalJSON, we can add this to better handle the response.
// Normally I wouldn't include this in the same class as the data-service though.
private interface GetResponse {
    _embedded: {
        heroes: Hero[];
        _links: {self: {href: string}};
    };
}

@Injectable({ providedIn: 'root' })
export class HeroService {

  private heroesUrl = 'http://localhost:8080/heroes';  // URL to web api

  httpOptions = {
    headers: new HttpHeaders({ 'Content-Type': 'application/json' })
  };

  constructor(
    private http: HttpClient,
    private messageService: MessageService) { }

}
```

### 5. Create call to retrieve heroes from backend

```
/** GET heroes from the server */
  getHeroes(): Observable<Hero[]> {
    return this.http.get<GetResponse>(this.heroesUrl)
    .pipe(
        map(response => response._embedded.heroes),
        tap(heroes => this.log(`fetched heroes`)),
        catchError(this.handleError('getHeroes', []))
    );
  }
```

### 6. Display Heroes

### 7. Create form for adding new hero

app.module.ts:

```
@NgModule({
  imports: [
    // other imports ...
    ReactiveFormsModule
  ],
})
```

Create a new FormGroup in HeroComponent:

```
heroForm: FormGroup;

constructor(
    _fb: FormBuilder
){}

buildForm(): void {
    heroForm = this._fb.group({
        name: new FormControl(''),
    });
}
```

Create a new form:

```
<div formGroupName="heroForm">
  <h2>Hero</h2>

  <label for="name">Name: </label>
  <input id="name" type="text" formControlName="name">
</div>
```

### 8. Create function for submission, and add unit tests

### 9. Add data service for POST

```
/** POST: add a new hero to the server */
  addHero(hero: Hero): Observable<Hero> {
    return this.http.post<Hero>(this.heroesUrl, hero, this.httpOptions).pipe(
      tap((newHero: Hero) => this.log(`added hero w/ id=${newHero.id}`)),
      catchError(this.handleError<Hero>('addHero'))
    );
  }
```
