## Connect to Mongo/Mongoose

```

$ npm install --save @nestjs/mongoose mongoose
$ npm install --save-dev @types/mongoose

```


####Import Mongoose Module
````
app.module.tsJS

import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';

@Module({
  imports: [MongooseModule.forRoot('mongodb://localhost/nest')],
})
export class AppModule {}
````

#### Model Define Schema

```
import * as mongoose from 'mongoose';

export const CatSchema = new mongoose.Schema({
  name: String,
  age: Number,
  breed: String,
});
```

### Provide the Model

```$xslt

@Module({
  imports: [MongooseModule.forFeature([{ name: 'Cat', schema: CatSchema }])],
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {}
```
### Inject the Model

```$xslt
@Injectable()
export class CatsService {
  constructor(@InjectModel('Cat') private catModel: Model<Cat>) {}

  async create(createCatDto: CreateCatDto): Promise<Cat> {
    const createdCat = new this.catModel(createCatDto);
    return createdCat.save();
  }

  async findAll(): Promise<Cat[]> {
    return this.catModel.find().exec();
  }
}

```
### To access the native Mongoose Connection object

```
@Injectable()
export class CatsService {
  constructor(@InjectConnection() private connection: Connection) {}
}
```
