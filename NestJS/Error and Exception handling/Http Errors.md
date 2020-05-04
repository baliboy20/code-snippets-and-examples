## HTTP ERROR HANDLING
```typescript
import {ArgumentsHost, Catch, ExceptionFilter, HttpException} from '@nestjs/common';

@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
    catch(exception: any, host: ArgumentsHost): any {
        console.log('inside the filter now...');
        const ctx = host.switchToHttp();
        const req = ctx.getRequest();
        const resp = ctx.getResponse();
        const statusCode = exception.status;
        return resp.status(statusCode).json({
            status: statusCode,
            createdBy: 'HttpExceptonfilter',
            message: exception.message.message,
        });
    }
} //
```
