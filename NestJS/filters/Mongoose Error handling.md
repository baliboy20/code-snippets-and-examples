##Mongoose Error Handling

### Mongoose Exception
Extend Mongoose Exception from HttpException
```typescript

export class MongooseException extends HttpException {
  constructor(response: string | Record<string, any>, status: number) {
  super(response, status);
  }
}

```

###MongooseExceptionFilter
```typescript
@Catch(MongooseException)
export class MongooseExceptionFilter implements ExceptionFilter{
  catch(exception: MongooseException, host: ArgumentsHost): any {
    console.log('MONGOOSE--> ', JSON.stringify(exception));
    const res = host.switchToHttp().getResponse();
    const req = host.switchToHttp().getRequest();
    const statusCode = exception.getStatus();
    const errMsg = exception.message;
    return res.status(statusCode).json(
      exception.getResponse(),
    )
  }
}
```


###Implementation in RepoService
using Try catch clock
```
  async add(timesheet: TimesheetItem) {
    const mdl = new this.model(timesheet);
    try {
      return await mdl.save();
    } catch (error) {
      throw new MongooseException({
        operation: 'add()',
        class: 'RepoService',
        error: error.message,
        statusCode: error.statusCode,
      }, 555);
    }
  }
```

###Configuration in Global handlers
main.ts
```typescript
  app.useGlobalFilters(
      new FallbackExpectonFilter(),
      new HttpExceptionFilter(),
      new MongooseExceptionFilter()
  )
```
