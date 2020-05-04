##Global Handling filter
global.filter.ts
```typescript
import {ArgumentsHost, Catch, ExceptionFilter} from '@nestjs/common';

@Catch()
export class FallbackExpectonFilter implements ExceptionFilter{
    catch(exception: any, host: ArgumentsHost): any {
        console.log('A general excepton has occuured', JSON.stringify(exception));
        const res = host.switchToHttp().getResponse();
        const req = host.switchToHttp().getRequest();
        const statusCode = exception.statusCode;
        const errMsg = exception.message;
        return res.status(500).json({
            statusCode: 500,
            createdBy: 'fallbackExceptionFilter',
            message: errMsg ? errMsg : 'unexpected exception has occurred',
        })
    }


}

```
