Below is a large collection of MEAN stack interview questions, ranging from beginner to advanced levels, grouped by each technology and some that span the entire stack. Each question includes an answer or explanation, and many include code samples for clarity. The idea is not only to test theoretical knowledge but also practical implementation details in MongoDB, Express, Angular, and Node.js.

---

## MongoDB (M)

**1. What is MongoDB and how does it differ from a relational database?**  
**Answer:**  
- MongoDB is a NoSQL document-oriented database storing data as BSON (binary JSON) documents.  
- Unlike relational databases (like MySQL), MongoDB does not use tables and rows. Instead, it uses collections and documents, offering a flexible schema that can evolve over time.  
- It does not enforce a strict schema, which allows for rapid iteration and changes to data models without costly schema migrations.

**2. How do you connect to MongoDB using Mongoose in a Node.js application?**  
**Answer (Code):**
```js
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost:27017/mydb', {
  useNewUrlParser: true,
  useUnifiedTopology: true
}).then(() => {
  console.log("Connected to MongoDB");
}).catch((err) => {
  console.error("Error connecting to MongoDB:", err);
});
```

**3. How do you define a Mongoose schema and model?**  
**Answer (Code):**
```js
const mongoose = require('mongoose');
const { Schema, model } = mongoose;

const userSchema = new Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  age: Number
});

const User = model('User', userSchema);
```

**4. How do you perform CRUD operations with Mongoose?**  
**Answer (Code):**
```js
// CREATE
const newUser = await User.create({ name: 'Alice', email: 'alice@example.com', age: 25 });

// READ
const foundUser = await User.findOne({ email: 'alice@example.com' });

// UPDATE
await User.updateOne({ email: 'alice@example.com' }, { $set: { age: 26 } });

// DELETE
await User.deleteOne({ email: 'alice@example.com' });
```

**5. What is an aggregation pipeline in MongoDB and give an example?**  
**Answer:**  
The aggregation pipeline is a framework for data aggregation modeled on the concept of data processing pipelines. It allows the transformation of documents into aggregated results using stages like `$match`, `$group`, `$project`.

**Answer (Code):**
```js
const results = await User.aggregate([
  { $match: { age: { $gte: 18 } } },
  { $group: { _id: "$age", count: { $sum: 1 } } },
  { $sort: { count: -1 } }
]);
```

**6. How do you create an index in MongoDB for better query performance?**  
**Answer (Code):**
```js
// Using Mongoose
userSchema.index({ email: 1 }, { unique: true });
await User.init(); // Ensure indexes are applied
```

**7. How do you handle relationships in MongoDB—embedding vs. referencing?**  
**Answer:**  
- **Embedding:** Store related data in the same document. Good for one-to-few, small and frequent read operations.  
- **Referencing:** Store ObjectIds as references to other collections. Better for large or frequently changing related data. Use `populate()` in Mongoose to fetch related documents.

**Answer (Code - Referencing):**
```js
const postSchema = new Schema({
  title: String,
  author: { type: Schema.Types.ObjectId, ref: 'User' }
});

// Later:
const post = await Post.findOne({ title: 'Hello World' }).populate('author');
```

**8. How do you implement transactions in MongoDB using Mongoose?**  
**Answer (Code):**
```js
const session = await mongoose.startSession();
session.startTransaction();
try {
  await User.updateOne({ _id: userId }, { $inc: { balance: -100 } }, { session });
  await Order.create([{ userId, amount: 100 }], { session });
  
  await session.commitTransaction();
} catch (err) {
  await session.abortTransaction();
} finally {
  session.endSession();
}
```

---

## Express.js (E)

**9. What is Express.js and why use it?**  
**Answer:**  
Express.js is a popular Node.js framework for building web and REST APIs. It simplifies the server creation process, routing, middleware handling, and provides a robust ecosystem of third-party middleware.

**10. How do you create a basic Express server?**  
**Answer (Code):**
```js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Hello Express!');
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

**11. How do you add middleware to Express?**  
**Answer (Code):**
```js
app.use(express.json()); // Parse JSON body
app.use((req, res, next) => {
  console.log(`Request: ${req.method} ${req.url}`);
  next();
});
```

**12. How do you handle errors in Express?**  
**Answer:**  
Create an error-handling middleware function with four arguments (err, req, res, next) and place it after all routes.

**Answer (Code):**
```js
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Something broke!' });
});
```

**13. How do you define routes in separate files using Express Router?**  
**Answer (Code):**
```js
// userRoutes.js
const { Router } = require('express');
const router = Router();

router.get('/', (req, res) => res.json([{ name: 'Alice' }]));
router.post('/', (req, res) => {/* create user */});

module.exports = router;

// app.js
const userRoutes = require('./userRoutes');
app.use('/users', userRoutes);
```

**14. How do you protect routes in Express using middleware (e.g., JWT auth)?**  
**Answer (Code):**
```js
const jwt = require('jsonwebtoken');
function authMiddleware(req, res, next) {
  const token = req.headers.authorization;
  if (!token) return res.status(401).json({ error: 'No token' });
  try {
    const user = jwt.verify(token.replace('Bearer ', ''), 'SECRET');
    req.user = user;
    next();
  } catch (err) {
    res.status(403).json({ error: 'Invalid token' });
  }
}
app.get('/protected', authMiddleware, (req, res) => res.send('Protected route'));
```

**15. How do you implement request validation in Express?**  
**Answer (Code):**
```js
const { body, validationResult } = require('express-validator');

app.post('/users',
  body('email').isEmail(),
  (req, res) => {
    const errors = validationResult(req);
    if(!errors.isEmpty()) return res.status(400).json({ errors: errors.array() });
    // proceed
    res.send('Valid email!');
});
```

---

## Angular (A)

**16. What is Angular and what problem does it solve?**  
**Answer:**  
Angular is a TypeScript-based front-end framework for building single-page applications. It provides a structured way to manage components, state, routing, forms, and HTTP requests, promoting scalability and maintainability.

**17. How do you create a new Angular project using the CLI?**  
**Answer (Command):**
```
ng new my-app
cd my-app
ng serve
```

**18. Explain the difference between a Component and a Module in Angular.**  
**Answer:**  
- **Module:** A container that groups related components, directives, pipes, and services. The root module (AppModule) bootstraps the application.  
- **Component:** A class with a template and logic that represents a portion of the UI.

**19. How do you bind data from the Component class to the template?**  
**Answer (Code):**
```typescript
// component.ts
export class MyComponent {
  title = 'Hello Angular';
}

// component.html
<h1>{{ title }}</h1>
```

**20. How do you handle two-way data binding in Angular forms?**  
**Answer (Code):**
```html
<input [(ngModel)]="username" />
<p>You typed: {{ username }}</p>
```

**21. How do you make an HTTP GET request from Angular to an API?**  
**Answer (Code):**
```typescript
import { HttpClient } from '@angular/common/http';

export class UserService {
  constructor(private http: HttpClient) {}

  getUsers() {
    return this.http.get('/api/users');
  }
}
```

**22. How do you implement routing in Angular?**  
**Answer (Code):**
```typescript
import { RouterModule, Routes } from '@angular/router';
const routes: Routes = [
  { path: 'users', component: UsersComponent },
  { path: '', redirectTo: 'users', pathMatch: 'full' }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {}
```

**23. What is Angular’s OnPush change detection strategy and why use it?**  
**Answer:**  
`ChangeDetectionStrategy.OnPush` tells Angular to check a component’s view only when its input properties change (by reference) or an event triggers it. This improves performance in large apps.

**24. How do you implement lazy loading in Angular?**  
**Answer (Code):**
```typescript
const routes: Routes = [
  { path: 'admin', loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule) }
];
```

**25. How do you handle forms validation in Angular (Reactive Forms)?**  
**Answer (Code):**
```typescript
this.form = this.fb.group({
  email: ['', [Validators.required, Validators.email]],
  password: ['', Validators.required]
});
```

---

## Node.js (N)

**26. What is Node.js and why is it good for building scalable network applications?**  
**Answer:**  
Node.js is a JavaScript runtime built on Chrome’s V8 engine. It uses an event-driven, non-blocking I/O model, making it lightweight and efficient for data-intensive real-time applications.

**27. Explain the Event Loop in Node.js.**  
**Answer:**  
The event loop is the core mechanism in Node.js that handles asynchronous events. It continuously checks the call stack, callback queues, and microtasks, executing callbacks and allowing Node.js to handle many connections efficiently on a single thread.

**28. How do you export and import modules in Node.js?**  
**Answer (Code):**
```js
// sum.js
function sum(a,b) { return a+b; }
module.exports = sum;

// app.js
const sum = require('./sum');
console.log(sum(2,3)); // 5
```

**29. How do you handle asynchronous operations using async/await?**  
**Answer (Code):**
```js
async function fetchData() {
  try {
    const data = await fetchSomeData();
    console.log(data);
  } catch(err) {
    console.error(err);
  }
}
```

**30. How do you use the `cluster` module in Node.js to utilize multiple CPU cores?**  
**Answer (Code):**
```js
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length;

if (cluster.isPrimary) {
  for (let i = 0; i < numCPUs; i++) cluster.fork();
} else {
  http.createServer((req, res) => res.end('Hello')).listen(3000);
}
```

---

## Full Stack MEAN Questions

**31. How do you integrate Angular’s front-end with an Express backend route?**  
**Answer:**  
- Angular runs on `http://localhost:4200` (default), Express on `http://localhost:3000`.
- Use HttpClient in Angular to hit `http://localhost:3000/api/users`.
- Configure CORS on the server if domains differ.

**Answer (Code):**
```typescript
// Angular service
this.http.get('http://localhost:3000/api/users').subscribe(console.log);
```

```js
// Express route
app.get('/api/users', async (req, res) => {
  const users = await User.find();
  res.json(users);
});
```

**32. How do you handle authentication with the MEAN stack using JWT?**  
**Answer (Steps):**  
- Generate a JWT on the server after a valid login.  
- Store it in localStorage or use an HttpOnly cookie on the client.  
- Angular includes the token in requests via an HttpInterceptor.  
- Express middleware verifies the token before allowing access to protected routes.

**Answer (Express Middleware Example):**
```js
function jwtAuth(req, res, next) {
  const token = req.headers.authorization;
  if(!token) return res.status(401).send('No token');
  try {
    const user = jwt.verify(token.replace('Bearer ', ''), 'SECRET');
    req.user = user;
    next();
  } catch {
    res.status(403).send('Invalid token');
  }
}
```

**33. How do you structure a MEAN project for scalability?**  
**Answer:**  
- Keep Angular code separate in its own project folder.  
- Backend (Node/Express) in `server/` directory.  
- Use environment variables for configuration.  
- Maintain separate routes, controllers, models folders for Express.  
- Implement services for database logic and keep controllers thin.

**34. How do you implement pagination on the server and consume it on the client?**  
**Answer (Code Server):**
```js
app.get('/api/users', async (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = 10;
  const skip = (page - 1)* limit;
  
  const users = await User.find().skip(skip).limit(limit);
  res.json(users);
});
```

**Answer (Code Client - Angular):**
```typescript
this.http.get(`/api/users?page=${this.currentPage}`).subscribe(users => {
  this.users = users;
});
```

**35. How do you handle file uploads in MEAN stack?**  
**Answer:**  
- Use `multer` in Express to handle file uploads.
- Angular uses a form with `FormData` to submit files.

**Answer (Express Code):**
```js
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });

app.post('/api/upload', upload.single('avatar'), (req, res) => {
  res.json({ file: req.file });
});
```

**Answer (Angular Code):**
```typescript
const formData = new FormData();
formData.append('avatar', this.selectedFile);
this.http.post('/api/upload', formData).subscribe(console.log);
```

---

## Advanced & Scenario-Based Questions

**36. How do you improve performance for a MEAN app under high load?**  
**Answer:**  
- Use `ChangeDetectionStrategy.OnPush` in Angular to reduce unnecessary re-renders.
- Implement indexing in MongoDB for faster queries.
- Add caching layers (Redis).
- Use clustering or load balancing for Node.js processes.
- Use lazy loading for Angular modules.

**37. How do you secure a MEAN application?**  
**Answer:**  
- Use HTTPS.
- Store JWT in HttpOnly cookies or use secure client-side storage.
- Sanitize and validate all inputs on the server.
- Implement role-based access control.
- Use `helmet` in Express and apply CSP headers.

**38. How would you set up SSR (Server-Side Rendering) with Angular Universal in MEAN?**  
**Answer:**  
- Use Angular Universal to pre-render Angular components on the server.
- The Node.js Express server serves the pre-rendered pages, improving SEO and initial load times.
- Use `ng add @nguniversal/express-engine` to set up Angular Universal and integrate with an Express server.

**39. How do you implement a microservices architecture with MEAN?**  
**Answer:**  
- Break down the backend into multiple microservices communicating via HTTP or message queues.
- The Angular front-end consumes APIs from multiple services.
- Use a gateway service (Express-based) to aggregate and route requests.
- Each microservice manages its own database and logic.

**40. How do you test a MEAN stack application end-to-end?**  
**Answer:**  
- Use Jasmine/Karma or Jest for Angular unit tests.
- Use Mocha/Chai or Jest + Supertest for Express API tests.
- For E2E, use Cypress or Playwright to simulate user interactions in a real browser environment.

---

## Code-Focused Deep-Dive Questions

**41. Show how to implement an Express middleware that measures request time.**  
**Answer (Code):**
```js
function requestTime(req, res, next) {
  const start = Date.now();
  res.on('finish', () => {
    const duration = Date.now() - start;
    console.log(`${req.method} ${req.url} took ${duration}ms`);
  });
  next();
}
app.use(requestTime);
```

**42. Demonstrate how to use Angular’s HttpInterceptor for adding a token to requests.**  
**Answer (Code):**
```typescript
@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler) {
    const token = localStorage.getItem('token');
    if (token) {
      const cloned = req.clone({ setHeaders: { Authorization: `Bearer ${token}` } });
      return next.handle(cloned);
    }
    return next.handle(req);
  }
}
```

**43. Show an example of a Mongoose model with custom validation.**  
**Answer (Code):**
```js
const userSchema = new Schema({
  username: {
    type: String,
    validate: {
      validator: v => /^[a-zA-Z0-9_]+$/.test(v),
      message: 'Username can only contain alphanumeric characters and underscores'
    },
    required: true
  }
});
```

**44. Provide an Angular example using Reactive Forms and a custom validator.**  
**Answer (Code):**
```typescript
function forbiddenNameValidator(forbiddenName: RegExp): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const forbidden = forbiddenName.test(control.value);
    return forbidden ? { forbiddenName: { value: control.value } } : null;
  };
}

// usage
this.form = this.fb.group({
  username: ['', [forbiddenNameValidator(/admin/i)]]
});
```

**45. Show a code example of handling asynchronous code with RxJS in Angular (e.g., switchMap).**  
**Answer (Code):**
```typescript
this.searchInput.valueChanges
  .pipe(
    debounceTime(300),
    distinctUntilChanged(),
    switchMap(query => this.http.get(`/api/search?q=${query}`))
  )
  .subscribe(results => this.results = results);
```

---

## Expert-Level Questions

**46. How do you implement caching at the database query level in a MEAN stack application?**  
**Answer:**  
- Use a cache like Redis.  
- Before querying MongoDB, check Redis if the data exists.  
- If yes, return cached data. If no, query MongoDB, then store results in Redis for future requests.

**Answer (Code Concept):**
```js
const cached = await redis.get('users');
if (cached) return JSON.parse(cached);

const users = await User.find();
await redis.set('users', JSON.stringify(users), 'EX', 3600);
return users;
```

**47. How do you implement SSR with Angular Universal and show a code snippet for Express integration?**  
**Answer (High-Level Code):**
```js
const { ngExpressEngine } = require('@nguniversal/express-engine');
const { AppServerModuleNgFactory } = require('./dist/server/main');

app.engine('html', ngExpressEngine({
  bootstrap: AppServerModuleNgFactory,
}));
app.set('view engine', 'html');
app.set('views', 'dist/browser');

app.get('*', (req, res) => {
  res.render('index', { req });
});
```

**48. Describe how you’d implement a CI/CD pipeline for a MEAN application.**  
**Answer:**  
- Commit triggers pipeline.  
- Run unit tests (Angular, Node tests).  
- Run integration tests with Supertest.  
- If tests pass, build Angular for production, build Docker images.  
- Deploy to staging environment, run E2E tests.  
- If successful, deploy to production.

**49. How do you handle rate limiting in Express to prevent abuse?**  
**Answer (Code):**
```js
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15*60*1000,
  max: 100
});

app.use('/api', limiter);
```

**50. How do you debug performance issues in a Node.js/Express application?**  
**Answer:**  
- Use `node --inspect` and Chrome DevTools for CPU profiling.  
- Use `clinic` tools (clinic flame, clinic bubbleprof) for event loop analysis.  
- Add logging around slow operations.  
- Check MongoDB query performance with `explain()` and add indexes as needed.

---

These questions cover a wide range of topics from basic concepts (beginners) to architectural decisions, performance optimizations, SSR, CI/CD, and security (advanced). The included code snippets demonstrate how to apply theoretical knowledge practically, which is often what interviewers look for.

Below are additional MEAN stack interview questions (continuing from the previous set, now starting at 51), including more advanced and scenario-based questions. As before, these questions include code examples and explanations that test depth of knowledge across MongoDB, Express, Angular, Node.js, as well as full-stack integration aspects.

---

## Additional MongoDB Questions

**51. How do you use the `$lookup` operator in MongoDB aggregation for joining two collections?**  
**Answer:**  
`$lookup` lets you perform a left outer join to another collection in the same database to filter in documents from the “joined” collection.

**Example (Code):**
```js
const results = await Order.aggregate([
  { $lookup: {
      from: 'users',
      localField: 'userId',
      foreignField: '_id',
      as: 'userInfo'
    }
  },
  { $unwind: '$userInfo' },
  { $project: { product: 1, 'userInfo.email': 1, 'userInfo.name': 1 } }
]);
```

**52. How would you implement a full-text search in MongoDB?**  
**Answer:**  
Create a text index on one or more fields, then use `$text` in your queries.

**Example (Code):**
```js
// Create a text index
await Product.collection.createIndex({ name: "text", description: "text" });

// Query using $text
const results = await Product.find({ $text: { $search: "coffee" } });
```

**53. Explain the difference between `$match` and `$project` in the aggregation pipeline.**  
**Answer:**  
- `$match`: Filters documents in the pipeline based on conditions, similar to a query’s `find()` filter.  
- `$project`: Shapes the documents, selecting which fields to include or exclude, and creating computed fields.

---

## Additional Express.js Questions

**54. How do you implement custom rate limiting logic in Express without a library?**  
**Answer:**  
You can store request timestamps in memory or a cache (like Redis) keyed by IP, and then manually check how many requests occurred in a given time window.

**Pseudo-Code:**
```js
const requests = {}; // { ip: [timestamps] }

app.use((req, res, next) => {
  const ip = req.ip;
  const now = Date.now();
  requests[ip] = (requests[ip] || []).filter(ts => now - ts < 60000);
  if (requests[ip].length > 100) {
    return res.status(429).json({ error: 'Too many requests' });
  }
  requests[ip].push(now);
  next();
});
```

**55. How do you implement file downloads from Express endpoints?**  
**Answer (Code):**
```js
app.get('/download/:filename', (req, res) => {
  const file = `${__dirname}/uploads/${req.params.filename}`;
  res.download(file); // triggers browser download
});
```

**56. How would you handle different environments (dev, staging, production) in an Express application?**  
**Answer:**  
Use environment variables and separate configuration files. For example, `dotenv` to load variables from `.env`, and switch logging or debugging modes based on `NODE_ENV`.

**Example (Code):**
```js
require('dotenv').config();
const env = process.env.NODE_ENV || 'development';
if(env === 'production') {
  // Production configs
} else {
  // Dev configs
}
```

---

## Additional Angular Questions

**57. What are Angular Pipes and how do you create a custom pipe?**  
**Answer:**  
Pipes transform data in templates. To create a custom pipe, implement the `PipeTransform` interface and decorate with `@Pipe`.

**Example (Code):**
```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'uppercaseFirst' })
export class UppercaseFirstPipe implements PipeTransform {
  transform(value: string): string {
    return value.charAt(0).toUpperCase() + value.slice(1);
  }
}

// Usage in template: {{ 'hello' | uppercaseFirst }} -> 'Hello'
```

**58. How do you handle component communication in Angular without using @Input() and @Output()?**  
**Answer:**  
Use a shared service with Observables/Subjects to share state and notify components. Components inject the service and subscribe to updates.

**Example (Code):**
```typescript
@Injectable({ providedIn: 'root' })
export class SharedService {
  private messageSource = new BehaviorSubject<string>('default');
  message$ = this.messageSource.asObservable();
  
  updateMessage(msg: string) {
    this.messageSource.next(msg);
  }
}
```

**59. How do you prefetch data before navigation in Angular using a `Resolver`?**  
**Answer:**  
Create a resolver class that implements `Resolve` and returns data (often using HttpClient). Associate the resolver with a route so data is fetched before the component loads.

**Example (Code):**
```typescript
@Injectable({ providedIn: 'root' })
export class UserResolver implements Resolve<User[]> {
  constructor(private userService: UserService) {}
  resolve(): Observable<User[]> {
    return this.userService.getUsers();
  }
}

// route definition
{ path: 'users', component: UsersComponent, resolve: { users: UserResolver } }
```

**60. Explain Angular’s `HttpClientInterceptor` chain order and how multiple interceptors work together.**  
**Answer:**  
Interceptors form a chain where the request goes through each interceptor in the order provided, and the response passes back through them in reverse order. Each interceptor can modify request and/or response.

---

## Additional Node.js Questions

**61. How do you handle large file processing in Node.js efficiently?**  
**Answer:**  
Use Streams. Read the file using a readable stream and process it chunk by chunk, avoiding loading the entire file into memory.

**Example (Code):**
```js
const fs = require('fs');
const readStream = fs.createReadStream('largefile.txt');
readStream.on('data', chunk => {
  // process chunk
});
readStream.on('end', () => {
  console.log('Done processing file.');
});
```

**62. How do you create a custom EventEmitter in Node.js and use it?**  
**Answer (Code):**
```js
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {}
const myEmitter = new MyEmitter();

myEmitter.on('greet', name => console.log(`Hello, ${name}!`));
myEmitter.emit('greet', 'Alice');
```

**63. Explain when you might use Worker Threads vs. the Cluster module in Node.js.**  
**Answer:**  
- **Worker Threads:** For CPU-bound tasks within a single process, allowing parallel execution without blocking the event loop.  
- **Cluster:** For load balancing multiple instances of an entire Node.js server, taking advantage of multi-core CPUs to handle more concurrent connections.

---

## Full Stack and Integration Questions

**64. How do you orchestrate requests in Angular that depend on data from multiple API calls?**  
**Answer:**  
Use RxJS operators like `forkJoin`, `combineLatest`, or `switchMap` to combine multiple Observables (Http calls) before rendering the component.

**Example (Code):**
```typescript
forkJoin([
  this.http.get('/api/users'),
  this.http.get('/api/settings')
]).subscribe(([users, settings]) => {
  this.users = users;
  this.settings = settings;
});
```

**65. What is CORS and how do you enable it in an Express server for an Angular client?**  
**Answer:**  
CORS (Cross-Origin Resource Sharing) allows your Angular app on `http://localhost:4200` to access an Express API on a different domain or port. Use the `cors` middleware.

**Example (Code):**
```js
const cors = require('cors');
app.use(cors({ origin: 'http://localhost:4200' }));
```

**66. How do you deploy a MEAN stack application to production?**  
**Answer:**  
- Build Angular app for production (`ng build --prod`).  
- Serve Angular’s static files from Express’s `public` directory or a dedicated static server (Nginx).  
- Configure Node.js server on a production machine (e.g., using PM2).  
- Set environment variables for production (DB URI, JWT secret).  
- Use a reverse proxy (Nginx) and configure HTTPS.

---

## More Advanced Scenarios

**67. How do you implement server-side pagination for a large dataset and ensure Angular updates the UI efficiently?**  
**Answer:**  
- On the server (Express + MongoDB), use `.skip()` and `.limit()` with query parameters `page` and `limit`.  
- On the Angular front-end, call the endpoint with the current page number, update the displayed subset of data.  
- Use `ChangeDetectionStrategy.OnPush` and a `trackBy` function in *ngFor to efficiently re-render lists.

**68. How would you integrate a GraphQL layer into a MEAN stack?**  
**Answer:**  
- Replace or complement Express REST endpoints with a GraphQL endpoint using libraries like `apollo-server-express`.  
- Angular queries the GraphQL endpoint with Apollo Client.  
- MongoDB queries still happen in resolvers on the Node.js server.

**Example (Code Snippet - Server):**
```js
const { ApolloServer, gql } = require('apollo-server-express');
const typeDefs = gql`type Query { users: [User] }`;
const resolvers = { Query: { users: () => User.find() } };
const server = new ApolloServer({ typeDefs, resolvers });
server.applyMiddleware({ app });
```

**69. How do you handle file uploads and processing at scale (large images or videos) in MEAN?**  
**Answer:**  
- Offload file storage to external services like AWS S3.  
- Use `multer` for upload, then stream the file directly to S3.  
- For Angular, show upload progress with HttpClient’s `reportProgress` option.  
- Process large files asynchronously using worker threads or a queue system.

**70. How do you implement real-time features (e.g., chat) in a MEAN application?**  
**Answer:**  
- Use Socket.io on the Node.js/Express backend.  
- Connect Angular to the Socket.io server.  
- Emit and listen to events for real-time updates.

**Example (Server Code):**
```js
const server = require('http').createServer(app);
const io = require('socket.io')(server);

io.on('connection', socket => {
  socket.on('message', msg => io.emit('message', msg));
});
```

**Example (Angular Client):**
```typescript
this.socket = io('http://localhost:3000');
this.socket.on('message', (msg: string) => { this.messages.push(msg); });
```

---

## Testing, CI/CD, and Security

**71. How do you write a unit test for an Angular component that uses HttpClient?**  
**Answer:**  
- Use `HttpClientTestingModule` and `HttpTestingController` to mock HTTP requests.  
- Setup TestBed with `HttpClientTestingModule`, inject `HttpTestingController`, and verify requests and responses.

**Example (Code):**
```typescript
TestBed.configureTestingModule({
  imports: [HttpClientTestingModule],
  declarations: [UsersComponent],
  providers: [UserService]
});

const httpMock = TestBed.inject(HttpTestingController);
```

**72. How do you unit test an Express route handler?**  
**Answer:**  
Use Supertest to make requests to your Express app and assert responses.

**Example (Code):**
```js
const request = require('supertest');
const app = require('../app');

it('GET /api/users should return 200 and array', async () => {
  const res = await request(app).get('/api/users');
  expect(res.statusCode).toBe(200);
  expect(Array.isArray(res.body)).toBe(true);
});
```

**73. How do you implement CI/CD for a MEAN app?**  
**Answer:**  
- Use a pipeline (e.g., GitHub Actions, GitLab CI, Jenkins) that:  
  - Installs dependencies  
  - Runs lint checks and unit tests (Angular + Node tests)  
  - Builds the Angular app for production  
  - Potentially runs integration/E2E tests  
  - Deploys to a staging environment and then production on success.

**74. How do you secure environment variables and secrets in a MEAN application?**  
**Answer:**  
- Use `.env` files locally (not committed).  
- In production, inject environment variables via the host environment or secret managers like Vault or AWS Secrets Manager.  
- Use HTTPS and ensure no secrets are exposed in the client code.

**75. How do you handle SQL-like aggregations or complex queries in MongoDB (e.g., joins)?**  
**Answer:**  
Use the aggregation pipeline with `$lookup`, `$unwind`, `$group`, `$project`, and `$match`. For advanced cases, consider using `$facet` or $unionWith. If relational complexity grows, consider referencing and using multiple queries rather than forcing complex joins.

---

## Performance and Optimization

**76. How do you optimize Angular bundle size?**  
**Answer:**  
- Use the `--prod` build (`ng build --prod`) for Ahead-of-Time compilation and tree-shaking.  
- Enable lazy loading for large modules.  
- Use `ChangeDetectionStrategy.OnPush` and pure pipes.  
- Analyze bundle using `ng build --stats-json` and `webpack-bundle-analyzer`.

**77. How do you optimize MongoDB queries in a MEAN application?**  
**Answer:**  
- Use indexes on frequently queried fields.  
- Avoid `$regex` on large datasets without indexes.  
- Use `.explain('executionStats')` to analyze queries and add necessary indexes.  
- Cache frequently accessed queries in Redis.

**78. How do you track down memory leaks in a Node.js application?**  
**Answer:**  
- Use `--inspect` and Chrome DevTools to take heap snapshots.  
- Use packages like `leakage` or `clinic heapprof`.  
- Compare snapshots before and after load tests to identify leaks.

---

## Advanced Architectural Patterns

**79. How do you implement a micro-frontend architecture with Angular for the front-end of a MEAN application?**  
**Answer:**  
- Use Module Federation or a micro-frontend framework to load multiple Angular apps together.  
- Each micro-frontend handles a specific domain and communicates with the API gateway.  
- Ensures independent deployability of front-end parts.

**80. How do you integrate a message queue (like RabbitMQ or Kafka) in a MEAN stack?**  
**Answer:**  
- Node.js services produce or consume messages.  
- Mongoose writes processed data to MongoDB based on consumed messages.  
- Angular may poll or use WebSockets to display updates from these async processes.

---

## Even More Scenarios and Patterns

**81. How would you handle database migrations in a MEAN application?**  
**Answer:**  
- MongoDB doesn’t require schema migrations, but schema changes might require data migrations.  
- Use migration tools like `migrate-mongo` to handle versioned changes to the database documents.  
- Store migration versions in a special collection and run migrations on deployment.

**82. How do you secure file uploads to prevent malicious files from affecting the server?**  
**Answer:**  
- Validate file types and sizes before saving.  
- Use antivirus scanning or external services.  
- Store files outside of the main application directory or use cloud storage.  
- Generate unique filenames and never trust user input for file paths.

**83. How do you handle multiple Angular environments (dev, staging, prod) efficiently?**  
**Answer:**  
- Use environment files like `environment.ts`, `environment.prod.ts`.  
- Switch configurations by building with `--configuration=production`.  
- Set API endpoints and keys in these environment files.

**84. How do you implement role-based access control in a MEAN stack application?**  
**Answer:**  
- Store roles in the user document in MongoDB.  
- On login, sign the JWT with user’s role.  
- In Express middleware, check the user’s role from JWT before allowing certain routes.  
- In Angular, hide or disable UI elements based on role.

**85. How do you detect slow queries or endpoints in a MEAN application?**  
**Answer:**  
- Add logging and timing middleware in Express.  
- Use performance profiling tools like New Relic, Datadog APM, or OpenTelemetry.  
- Check MongoDB query times with `explain()` and add needed indexes.

---

## Testing and Quality

**86. How do you ensure code quality in a MEAN project?**  
**Answer:**  
- Use linters (ESLint for JS/TS) and formatters (Prettier).  
- Enforce test coverage thresholds.  
- Run automated tests in CI.  
- Code reviews and pull request checks.

**87. How do you test Angular components that use the router?**  
**Answer:**  
- Use `RouterTestingModule` to mock routing.  
- Provide mock ActivatedRoute data to test route parameters or resolvers.

---

## DevOps and Deployment

**88. How do you set up HTTPS for your MEAN stack in production?**  
**Answer:**  
- Use Nginx or a load balancer (like AWS ELB) to terminate SSL and forward traffic to Node.js.  
- Or have Node.js use HTTPS directly by providing SSL certificates to the `https` module.

**89. How do you implement rolling updates or blue-green deployments for a MEAN app?**  
**Answer:**  
- Use Kubernetes rolling updates or blue-green strategies.  
- Build new Docker image, deploy to a new set of pods, switch traffic over.  
- On success, remove old version pods.

**90. How do you handle logging and monitoring for a MEAN application at scale?**  
**Answer:**  
- Use a structured logger (Winston, Pino) in Node.js.  
- Send logs to ELK stack (ElasticSearch, Logstash, Kibana) or use a hosted logging solution.  
- Monitor frontend errors with a service like Sentry.

---

## Security Advanced

**91. How do you implement CSRF protection in a MEAN stack app?**  
**Answer:**  
- For traditional server-rendered pages, use CSRF tokens (e.g., via `csurf` middleware in Express).  
- For SPAs using JWT in headers, CSRF is less of an issue if no cookies are used or only HttpOnly cookies are used. You can also implement a same-site cookie policy.

**92. How do you prevent NoSQL injection attacks in MongoDB queries?**  
**Answer:**  
- Validate and sanitize user input.  
- Avoid directly inserting user input into query objects.  
- Use libraries like `mongo-sanitize`.

**Example (Code):**
```js
const sanitize = require('mongo-sanitize');
app.get('/api/users', (req, res) => {
  const q = sanitize(req.query.q);
  User.find({ name: q }).then(users => res.json(users));
});
```

---

## Scaling and Distributed Systems

**93. How would you scale the MongoDB layer in a MEAN application?**  
**Answer:**  
- Use a Replica Set for high availability.  
- Shard the database if dataset becomes very large.  
- Use appropriate shard keys and horizontal scaling to handle large loads.

**94. How do you implement caching strategies (e.g., LRU, time-based) for API responses?**  
**Answer:**  
- Use a cache store like Redis.  
- Implement a middleware that checks for a cached response before hitting the database.  
- Set expiration times (TTL) and possibly implement an LRU cache library in memory if data is small.

---

## Code Examples & Patterns

**95. How do you create a custom Angular directive to change background color on hover?**  
**Answer (Code):**
```typescript
@Directive({ selector: '[appHoverColor]' })
export class HoverColorDirective {
  @HostListener('mouseenter') onMouseEnter() {
    this.el.nativeElement.style.backgroundColor = 'yellow';
  }
  @HostListener('mouseleave') onMouseLeave() {
    this.el.nativeElement.style.backgroundColor = null;
  }
  constructor(private el: ElementRef) {}
}
```

**96. Show how to implement unit tests for a Mongoose model’s static method.**  
**Answer (Code):**
```js
// userModel.test.js
const User = require('./User');
describe('User model', () => {
  it('should find users by email', async () => {
    await User.create({ email: 'test@example.com', name: 'Test' });
    const user = await User.findByEmail('test@example.com');
    expect(user.name).toBe('Test');
  });
});
```

Assuming `findByEmail` is defined as a static method on the schema.

---

## More Complex Integration

**97. How do you handle partial updates to documents (PATCH operations) in MEAN?**  
**Answer:**  
Use `app.patch` in Express and `$set` in MongoDB.  
In Angular, send a PATCH request with only changed fields.

**Example (Server Code):**
```js
app.patch('/api/users/:id', async (req, res) => {
  const updates = req.body; // {age:30}
  const user = await User.findByIdAndUpdate(req.params.id, { $set: updates }, { new: true });
  res.json(user);
});
```

**98. How do you implement a multi-step form in Angular that saves intermediate steps to MongoDB?**  
**Answer:**  
- Store the form progress in a service.  
- On each step completion, send a partial update to the backend.  
- Backend updates the user’s “in-progress form” document in MongoDB.

**99. How do you ensure consistency between the front-end routes and backend endpoints?**  
**Answer:**  
- Use a shared configuration file or constants for endpoints.  
- Document endpoints with OpenAPI/Swagger.  
- Angular services and Express routes should refer to a consistent set of base URLs.

**100. How do you implement server-side rendering (SSR) caching for Angular Universal pages in a MEAN stack?**  
**Answer:**  
- Use a caching layer (Redis) or in-memory cache in the Express SSR server.  
- When a request comes in, check if the rendered page HTML is cached. If yes, serve it. If not, render via Universal and store the result in cache.

---

These additional questions (51-100) cover even more detailed scenarios, best practices, security, testing, performance optimization, and advanced architectural considerations that MEAN developers may face. Each question and answer aims to provide insights and code snippets helpful in real-world interviews and day-to-day development challenges.