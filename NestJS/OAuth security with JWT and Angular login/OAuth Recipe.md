#Recipe for OAuthSecurity Angular/NestJs

##Angular Front Frontend

###Login Window Component


####Http Service Component
  * Add User
  
````
    addUser(creds: { pw: string, email: string }) {
       this.http.post('http://localhost:3000/login/new-user', creds)
         .subscribe(
           (result) => this.sb.open('User added'),
   
           (err) => this.sb.open(err.message),
         );
     }
````
  The new user credentials are sent to the open login api and the user credential added with permissions to
  the admin database. The password is salted using  the user login and a server side secret. Once added
  a Jwt is created
  ### Intercept Response LoginInterceptor
   ```
   import { HttpEvent, HttpHandler, HttpInterceptor, HttpRequest, HttpResponse } from '@angular/common/http';
   import { Observable, pipe } from 'rxjs';
   import { map } from 'rxjs/operators';
   
   export class OAuthInteceptor implements HttpInterceptor {
     intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
       next.handle(req)
         .pipe(map(this.filterOnRepsonse))
       const jwt = localStorage.getItem('oauthToken');
       const cloned = req.clone({ headers: req.headers.set('Authorization', jwt) });
       return next.handle(cloned);
     }
   
     filterOnRepsonse(a) {
       if (a instanceof HttpResponse
         && a.status === 201
         && a.url === 'http://localhost:3000/login') {
         localStorage.setItem('oauthToken', a.body.passwordHash);
       }
       return a;
     }
   
     attachJwtToken(req: HttpEvent<any>) {
       let clonedResponse;
       console.log(
         'req.urlWithParams.includes(login)',
       );
       if (req instanceof HttpRequest
         && !req.url.includes('login')) {
         const jwt = localStorage.getItem('oauthToken');
   
         if (jwt) {
           clonedResponse = req.clone({ headers: req.headers.set('Authorizaton', `${jwt}`) });
         }
       }
       return clonedResponse;
     }
   }
   ```
## Server Side

#### Login Controller
    ````
        import { Body, Controller, Get, HttpException, Param, Post, UnauthorizedException } from '@nestjs/common';
        import * as password from 'password-hash-and-salt';
        import * as jwt from 'jsonwebtoken';
        import { DaoService, UserVo } from '../../repo/dao/dao.service';
        
        @Controller('login')
        export class LoginController {
          constructor(private dao: DaoService) {
          }
        
          @Post()
          async login(@Body() body: any) {
            return new Promise((resolve, reject) => {
              this.dao.findUserAndCompareVerifyPassword(body)
                .then((user: UserVo) => {
                  console.log('user BE', user.passwordHash);
                  const jwtoken = jwt.sign({email: user.email, roles: user.roles}, JWT_SECRET);
                  resolve({passwordHash: jwtoken});
                }).catch(error => {
                  console.log('are we here:?')
                reject(error);
              });
            });
          }
        
          @Post('add-user')
          async addUser(@Body('pw') pw, @Body('email') email: any) {
            console.log('pw: ', pw);
            return new Promise((resolve, reject) => {
              password(pw).hash((err, hash) => {
                const retval = this.dao.addUser({ passwordHash: hash, email });
                resolve({ message: 'User added' });
              });
        
            });
          }
        
          @Post('new-user')
          async newUser(@Body() body) {
            const retval = await this.dao.addUser(body);
            return retval;
          }
        
          @Get('exists/:email')
          async isUser(@Param('email') email: string) {
            ;
            const retval = await this.dao.exists(email);
            return { [email]: retval };
          }
        
          private async _newHashPW(pw: string) {
            return new Promise((resolve, reject) => {
        
              password(pw).hash((err, hash) => {
                if (err || !hash) {
                  reject('Hash password failed');
                } else {
                  resolve(hash);
                }
              });
            });
          }
        
          private async hasUser(email) {
            const exists = this.dao.exists(email);
            return exists;
          }
        }
        //should put this in the constants file
        export const JWT_SECRET = 'mysillysecret';
    ````


####MiddleWare to extract userRoles from JWT and verify the token.

    ```typescript
    import { Injectable, NestMiddleware, Request } from '@nestjs/common';
    import { JWT_SECRET } from '../../auth/login/login.controller';
    import * as jwt from 'jsonwebtoken';
    import { IncomingMessage } from 'http';
    
    @Injectable()
    export class GetUserMidddlewareService implements NestMiddleware {
      use(req: Request, res: any, next: () => void): any {
        const token = req.headers['authorization'];
        if (!token) {
          next();
          return;
        }
        try {
        const result = jwt.verify(token, JWT_SECRET);
          req['user'] = result;
       } catch(err) {
        }
    
        next();
      }
    }

    ```
Apply middleware within the App Module:- 
    ```markdown
    export class AppModule implements NestModule {
      configure(consumer: MiddlewareConsumer): any {
        consumer.apply(GetUserMidddlewareService)
          .forRoutes(AppController);
      }
    }
    ```

#### Use an Authorisation Guard to check privledges

  authentication-guard
    
    import { CanActivate, ExecutionContext, ForbiddenException, Injectable } from '@nestjs/common';
    import { Observable } from 'rxjs';
    import { InjectModel } from '@nestjs/mongoose';
    
    @Injectable()
    export class AuthorisationGuard implements  CanActivate {
      constructor(private allowedRoles: [string]) {
      }
      canActivate(context: ExecutionContext): boolean | Promise<boolean> | Observable<boolean> {
        const host = context.switchToHttp();
        const request = host.getRequest();
        const user = request['user'];
        const allowed = this.isAllowed(user.roles);
        if(!allowed) {
          throw new ForbiddenException(`User doesn't have sufficent access rights`);
        }
        return true;
      }
    
      private isAllowed(userRoles: [string]) {
        let allowed = false;
        userRoles.forEach(role => {
          if(this.allowedRoles.includes(role)) {
            allowed = true;
          }
        })
        return allowed;
      }
    
    }
    ```

### Apply the Authorisation guard on regular Http Request
    
     @Get('do-something')
      @UseGuards(AuthenticationGuardService)
     async getDoSomething(@Req() req: IncomingMessage) {
        console.log('req', req['user']);
        return {msg:'I am very busy now'};
      }
    }
    
