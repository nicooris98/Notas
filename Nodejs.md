# Crear package.json

Dentro de server ejecutar

```bash
npm init --yes
```

# Instalar modulos

```bash
npm i express morgan mysql2 cors
```

Esto crea la carpeta node_modules y el package.json.

# Crear carpeta src

src, que es para el codigo de la aplicacion.

## Dentro de src

Crear index.ts

# Instalar typescrpit

Dentro de server

```bash
npm install -g typescript
```

## Crear archivo de configuracion para typescript

```bash
tsc --init
```

## Modificar tsconfig.json

Descomentar la linea con outDir y darle valor build

```json
"outDir": "./build"
```

Luego escribimos 

```bash
tsc
```

y vemos que ahora tenemos la carpeta build

# Agregar comandos en package.json

En la parte de scripts hay que agregar:

```json
"scripts": {
    "build": "tsc -w",
    "dev": "nodemon build/index.js"
  }
```

# Instalar nodemon

```bash
npm i nodemon -D
```

## Iniciar estos 2 comandos en la carpeta server

```bash
npm run build
```

```bash
npm run dev
```

## Instalar los tipos

```bash
npm i @types/express -D
```

```bash
npm i @types/morgan @types/cors -D
```

# Crear carpeta controllers y archivos database.ts y keys.ts

En database va la conexion y en keys las propiedades para entrar

Fijatese en el video de Fazt para mas info.
