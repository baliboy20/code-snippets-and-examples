##Handling Vanilla Http Requests


###1.0 Endpoints
```
@Controller('say-hello')
export class SayHelloController {
    @Get('') getVanillaResponse  ()
    {
        return 'Miss Vanilla';
    }
....
```



###2.0 Parameterised
```$xslt
    @Get('/:name')
    getSayHelloWithParm(@Param('name') name: string) {
        return 'Good golly Miss Molly ' + name;
    }
```
```
GET http://localhost:3000/say-hello/jenny
Accept: */*
Cache-Control: no-cache
```

###3.0 Query String
``` 
    @Get('')
    getQueryStringResponse(@Query() req : string)  {
        console.log('req', req);
        return `Miss ${req['name']} is ready to see you.`;
    }
```

Using WS httpClient,
```
GET http://localhost:3000/say-hello?name=jenny
Accept: */*
Cache-Control: no-cache
```

###4. Body content
```
   @Post('')
    postName(@Body('name') body: any) {
        return 'ok' + body;

    }
```
Using WS httpClient,
```
POST http://localhost:3000/say-hello
Content-Type: application/json

{
  "name": "judy"
}
```

