## Simple Pipes for data transformations

####The Pipe class
```typescript
import {Body, Controller, Get, Post, UsePipes} from '@nestjs/common';
import {TutorialPipe} from '../../pipes/tutorial.pipe';

@Controller('tutorial-pipes')
export class TutorialPipesController {
    @Get()
    async getTutorialPipes() {
        return 'rabbit meat';
    }

    @Post()
    @UsePipes(new TutorialPipe())
    async postTutorial(@Body() data: { name: string }) {
        data.name = 'Cedric';
        return data;
    }
}
```

#### Use in the controller
```typescript
@Controller('tutorial-pipes')
export class TutorialPipesController {
    @Get()
    async getTutorialPipes() {
        return 'rabbit meat';
    }

    @Post()
    @UsePipes(new TutorialPipe())
    async postTutorial(@Body() data: { name: string }) {
        data.name = 'Cedric';
        return data;
    }
}
```
## Simple Pipes for data validations
install the packages
```
npm i --save class-validator class-transformer
```

####Create class definition of Vo
```typescript
import {Contains, IsDate, IsEmail, IsFQDN, IsInt, isInt, isString, Length, Max, Min} from 'class-validator';
export class PipeTutorialVo {
    @Length(10, 20)
    name: string;
    @IsInt()
    @Min(21)
    @Max(65)
    age: number;
}
```

####Create the Pipe
```typescript
@Injectable()
export class TutorialValidationPipe implements PipeTransform {
constructor() {
    console.log('validator bein g constructed?')
}
    async transform(value: any, {metatype}: ArgumentMetadata) {

        // if (!metatype || !this.toValidate(metatype)) {
        //     console.log('inside the tutvalpipe', value);
        //     return value;
        // }
        const vo = plainToClass(metatype, value);
        const errors = await validate(vo);
        if (errors.length > 0) {
            throw new BadRequestException('validation failed', errors.join(','));
        }
        return value;
    }

    private toValidate(metatype: Function): boolean {
        const types: Function[] = [String, Boolean, Number, Array, Object];
        return !types.includes(metatype);
    }
}
```

####Usage in the Controller
```typescript
 @Post()
    @UsePipes(TutorialValidationPipe)
    async postTutorial(@Body() data: PipeTutorialVo) {
        data.name = 'Cedrics';
        return data;
    }
```
