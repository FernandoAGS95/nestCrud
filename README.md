
# Mini Crud Nest con Cockroach

## dependencias a instalar
```
npm install --save @nestjs/swagger
npm i --save class-validator class-transformer
npm i --save typeorm
postgre porque cockroach trabaja en base a el
npm install --save typeorm postgresql
npm i --save @nestjs/config
npm install --save joi
npm install bcrypt
npm i -D @types/bcrypts
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
  
   3. Como anteriormente se ha borrado el updateNombreModuloDto tendremos que crearlo ahora, pero primero declaremos la clase CreateBuildingDto.
      -  la cual hara uso del class validator para revisar que nuestros strings no esten ''  y que obligadamente sean del tipo que necesitemos que sean. ( No olvidar             importarlos desde class-validator).
      - **Ambos Dto seran classes y no interfaces para asi tener acceso a las funciones de class-validator
      - **No se rellenaran todos los campos, pero son los mismos que se ven en el entity
      ```
      
      @IsString()
      @IsNotEmpty()
      direccion:string;
      
      @IsNumber()
      numero:number
      
      ```
   4. Una vez creado el DTO para la creacion de elementos, debemos crear la siguiente clase para el update
    - Como ya tenemos listo el create y este usa los mismos datos que el Update , podemos usar el PartialType, entregando en su argumento la clase CreateBuildingDto
    - Lo que hace PartialType es anteponer un ? para cada uno de nuestros atributos, haciendo de ellos opcionales, por lo cual contendra las validaciones de numero y que no este vacio pero tambien seran opcionales, en caso de que un usuario altere uno o dos campos, cuya accion no generara un error.

    ```
      export class UpdateBuildingStDto extends PartialType(CreateBuildingStDto)
    ```
 
 ### Antes de continuar con el Service
   ** En este proyeto estaremos trabajando con la DB cockroach, por lo cual ahora comenzaremos a definir la configuracion y los .env que no se adjuntan en este proyecto, debes hacerlo por tu cuenta
   
   1. Crear un .env que contenga los siguientes datos para preparar la conexion a la BD
       ```
          DB_PORT=
          DB_NAME=
          DB_USER=
          DB_PASS=
          DB_NAME=
          DB_ID=
       ```
    
   2.Una vez Creada el .env procedermos a crear nuestro config.ts dentro del src, el archvio config.ts  sera quien nos indicara los elementos que tenemos que ingresar para poder crear nuestras tablas, agregar filas y definir columnas 
   
      ```
      import { registerAs } from '@nestjs/config';

        export default registerAs('config', () => {
            return {
              database: {
                host: process.env.DB_HOST,
                port: +process.env.DB_PORT,
                username: process.env.DB_USER,
                password: process.env.DB_PASS,
                database: process.env.DB_NAME,
                extra: {
                    options: `--cluster=${process.env.DB_ID}`,
                },
            },
            database2: {},
            apiKey: process.env.API_KEY,
            };
        });
      ```
      
   3.
   
  


