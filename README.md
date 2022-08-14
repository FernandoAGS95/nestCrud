<p align="center">
  <a href="http://nestjs.com/" target="blank"><img src="https://nestjs.com/img/logo-small.svg" width="200" alt="Nest Logo" /></a>
</p>

[circleci-image]: https://img.shields.io/circleci/build/github/nestjs/nest/master?token=abc123def456
[circleci-url]: https://circleci.com/gh/nestjs/nest

  <p align="center">A progressive <a href="http://nodejs.org" target="_blank">Node.js</a> framework for building efficient and scalable server-side applications.</p>
    <p align="center">
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/v/@nestjs/core.svg" alt="NPM Version" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/l/@nestjs/core.svg" alt="Package License" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/dm/@nestjs/common.svg" alt="NPM Downloads" /></a>
<a href="https://circleci.com/gh/nestjs/nest" target="_blank"><img src="https://img.shields.io/circleci/build/github/nestjs/nest/master" alt="CircleCI" /></a>
<a href="https://coveralls.io/github/nestjs/nest?branch=master" target="_blank"><img src="https://coveralls.io/repos/github/nestjs/nest/badge.svg?branch=master#9" alt="Coverage" /></a>
<a href="https://discord.gg/G7Qnnhy" target="_blank"><img src="https://img.shields.io/badge/discord-online-brightgreen.svg" alt="Discord"/></a>
<a href="https://opencollective.com/nest#backer" target="_blank"><img src="https://opencollective.com/nest/backers/badge.svg" alt="Backers on Open Collective" /></a>
<a href="https://opencollective.com/nest#sponsor" target="_blank"><img src="https://opencollective.com/nest/sponsors/badge.svg" alt="Sponsors on Open Collective" /></a>
  <a href="https://paypal.me/kamilmysliwiec" target="_blank"><img src="https://img.shields.io/badge/Donate-PayPal-ff3f59.svg"/></a>
    <a href="https://opencollective.com/nest#sponsor"  target="_blank"><img src="https://img.shields.io/badge/Support%20us-Open%20Collective-41B883.svg" alt="Support us"></a>
  <a href="https://twitter.com/nestframework" target="_blank"><img src="https://img.shields.io/twitter/follow/nestframework.svg?style=social&label=Follow"></a>
</p>
  <!--[![Backers on Open Collective](https://opencollective.com/nest/backers/badge.svg)](https://opencollective.com/nest#backer)
  [![Sponsors on Open Collective](https://opencollective.com/nest/sponsors/badge.svg)](https://opencollective.com/nest#sponsor)-->

## Description

[Nest](https://github.com/nestjs/nest) framework TypeScript starter repository.

## Installation

```bash
$ npm install
```

## Running the app

```bash
# development
$ npm run start

# watch mode
$ npm run start:dev

# production mode
$ npm run start:prod
```

## Test

```bash
# unit tests
$ npm run test

# e2e tests
$ npm run test:e2e

# test coverage
$ npm run test:cov
```

## Support

Nest is an MIT-licensed open source project. It can grow thanks to the sponsors and support by the amazing backers. If you'd like to join them, please [read more here](https://docs.nestjs.com/support).

## Stay in touch

- Author - [Kamil Myśliwiec](https://kamilmysliwiec.com)
- Website - [https://nestjs.com](https://nestjs.com/)
- Twitter - [@nestframework](https://twitter.com/nestframework)

## License

Nest is [MIT licensed](LICENSE).



# Mini Crud Nest con Cockroach

## dependencias a instalar
```
npm install --save @nestjs/swagger
npm i --save class-validator class-transformer
npm i --save typeorm
postgre porque cockroach trabaja en base a el
npm install --save typeorm postgresql
```
 - Utilizaremos typeOrm para conectar nuestro cluster con nuestro crud

## Creacion de modulos 
  Procedemos a crear el modulo building-site que llevara nuestro crud 
```
nest g resource building-site
```

## Eliminacion de archivos extra
- Borraremos el app.controller.sec.ts
- Borraremos  app.controller.ts
- Borraremos app.service.ts

## Intervencion en main.ts para setear nuestros pipes
- Dentro de main.ts agregaremos el siguiente codigo luego de instanciar app
  	```
    app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      forbidNonWhitelisted: true,
    }),
  );
     ```
 - Tambien habra que impoart ValidationPipe de @nestjs/common

  ```
  import {ValidationPipe} from '@nestjs/common';
  ```
  - Lo que hace whitelist es : 
  - Lo que hace forbidNonWhitelisted es : 

### Primeros pasos
 ## Creacion del Entity
 
   1.Despues de configurar el main.ts tendremos que limpiar algunas secciones del controller
   En el controller debemos agregar ParseUUID en cada instancia donde encontremos el id
   Ademas debemos eliminar el Update$NombreDelModuloDTO que se genera automaticamente ya que este estara dentro del DTO createElement
   
    ```
    Antes 
    @Get(':id')
    getById(@Param('id') id: number) {
      return this.buildingSiteService.findOne(+id);
    }
    
    Despues 
    @Get(':id')
    getById(@Param('id', ParseUUIDPipe) id: string) {
      return this.buildingSiteService.findOne(id);
     }
    ```
  2. Luego de estas modificaciones, comenzaremos a crear nuestra Primera entidad building-site.entity.ts
    - Dentro del Entity declaramos que nuestro  id sera un string y le pondremos el decorador PrimaryGenetaredColumn('uui')
       De esta forma genera un uui por cada nuevo elemento ingresado de manera automatica
       
       ```
       @PrimaryGeneratedColumn('uui')
       id:string;
       ```
       
   - El resto de los elementos usaran el decorador @Column  y dependiendo del tipo de variable habra que poner dentro del parametro de column si es varchar, en este         caso agregar el length, en cuanto a los int, solo queda declarar su type.
    
       ```
        @Column({type:'varchar', length:255})
        direction:string;
        
        
        @Column({type:'int'})
        telefono:number;
       ```
    
     - Junto estos campos al final agregaremos dos campos extra para saber cuando se creo el elemento y cuando se ha realizado algun update
    
      ```
      @CreateDateColumn({type:'timestamptz,default: ()=> 'CURRENT_TIMESTAMP'})
      createAt:string;
      
      @UpdateDateColumn({type:'timestamptz,default: ()=> 'CURRENT_TIMESTAMP'})
      updateAt:string;
      ```
      
  ### Trabajando en el DTO
  
   3. 
 
  


