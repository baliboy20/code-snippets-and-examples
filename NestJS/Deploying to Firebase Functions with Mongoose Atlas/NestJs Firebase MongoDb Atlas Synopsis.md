##NestJs Firebase MongoDb Atlas Juicy parts.md
####1) Package.json

note added some build scripts for fire functions
and packages for mongodb/mongoose in Nestjs

```typescript
{
  "name": "zznest-cloudfunctions",
  "version": "0.0.1",
  "description": "",
  "author": "",
  "private": true,
  "license": "UNLICENSED",
  "main": "dist/index.js",
  "engines": {
    "node": "10"
  },
  "scripts": {
    "prebuild": "rimraf dist",
    "build": "nest build",
    "format": "prettier --write \"src/**/*.ts\" \"test/**/*.ts\"",
    "start": "npm run shell",
    "start:dev": "nest start --watch",
    "start:debug": "nest start --debug --watch",
    "start:prod": "node dist/main",
    "lint": "eslint \"{src,apps,libs,test}/**/*.ts\" --fix",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:cov": "jest --coverage",
    "test:debug": "node --inspect-brk -r tsconfig-paths/register -r ts-node/register node_modules/.bin/jest --runInBand",
    "test:e2e": "jest --config ./test/jest-e2e.json",
    "serve": "npm run build && firebase serve --only functions",
    "shell": "npm run build && firebase functions:shell",
    "deploy": "firebase deploy --only functions",
    "logs": "firebase functions:log"
  },
  "dependencies": {
    "@nestjs/common": "^7.0.0",
    "@nestjs/core": "^7.0.0",
    "@nestjs/platform-express": "^7.0.0",
    "firebase-admin": "^8.6.0",
    "firebase-functions": "^3.3.0",
    "helmet": "^4.1.0",
    "reflect-metadata": "^0.1.13",
    "rimraf": "^3.0.2",
    "rxjs": "^6.5.4",
    "@types/mongoose": "^5.7.8",
    "@nestjs/mongoose": "^6.4.0",
    "mongoose": "^5.9.6"
  },
  "devDependencies": {
    "@nestjs/cli": "^7.0.0",
    "@nestjs/schematics": "^7.0.0",
    "@nestjs/testing": "^7.0.0",
    "@types/express": "^4.17.3",
    "@types/jest": "25.1.4",
    "@types/node": "^13.9.1",
    "@types/supertest": "^2.0.8",
    "@typescript-eslint/eslint-plugin": "^2.23.0",
    "@typescript-eslint/parser": "^2.23.0",
    "eslint": "^6.8.0",
    "eslint-config-prettier": "^6.10.0",
    "eslint-plugin-import": "^2.20.1",
    "jest": "^25.1.0",
    "prettier": "^1.19.1",
    "supertest": "^4.0.2",
    "ts-jest": "25.2.1",
    "ts-loader": "^6.2.1",
    "ts-node": "^8.6.2",
    "tsconfig-paths": "^3.9.0",
    "typescript": "^3.7.4"
  },
  "jest": {
    "moduleFileExtensions": [
      "js",
      "json",
      "ts"
    ],
    "rootDir": "src",
    "testRegex": ".spec.ts$",
    "transform": {
      "^.+\\.(t|j)s$": "ts-jest"
    },
    "coverageDirectory": "../coverage",
    "testEnvironment": "node"
  }
}

```
####2) Configure AppModule for user with Atlas

```typescript
@Module({
  imports: [
    LocationsModule,
    MongooseModule.forRoot('mongodb+srv://williamPaulton:NuJ611J2VwyzgOeF@cluster0-dp1zo.mongodb.net/london-places?retryWrites=true&w=majority')

  ],
  controllers: [AppController, FrenchVocabController],

  providers: [AppService],
})
export class AppModule {}
```
###3) Enable cors and use Helmet
```typescript
import helmet = require('helmet');

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.use(helmet());
  app.enableCors({origin: true, methods: 'GET, PUT, POST, DELETE'})
  await app.listen(3000);
```
#### 4) Deploy to Firebase functions 
see pdf Setting up NestJs with Firebase functions.pdf
