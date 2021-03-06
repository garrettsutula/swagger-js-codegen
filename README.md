# simple-swagger-codegen
Minimalist Swagger > Javascript code generation from mustache templates - built-in node/express/swagger & Angular 6 codegens. Assumes linting, style, and complexity enforcement in mustache template or as part of CI process. Templates implemented so far:

- App
- Controller
- Module
- package.json

## TODO
- generate whole app from templates
- flatten other objects (operation security, responses, schema, scopes, api key, definitions properties)
- add to npm
- add tests
- add Angular 6 codegen

## Installation
Download/clone this repo for now using `curl`, `git` or your favorite github client app. Requires Node.js version `10.0` or higher. Use [nvm](https://github.com/creationix/nvm) to easily switch between different versions of node runtimes.

## Usage

Run the command `node --experimental-modules .\lib\cli.mjs <outputFile> -t [mustache template name] -s [swagger template name]` to generate `<outputFile>.js`

## Example

Example outputs in `./dist`, run the command `node --experimental-modules demoSwaggerApp.mjs|demoSwaggerController.mjs|demoSwaggerModule.mjs|demoSwaggerPackage.mjs` to re-generate the code.

CLI Example: `node --experimental-modules .\cli.mjs generate test -s '../templates/swagger.json' -t '../templates/classGenerator.mustache'`

```javascript
import fs from 'fs';
import getCode from 'codegen';

const swaggerFile = JSON.parse(fs.readFileSync('./templates/swagger.json', 'utf-8'));
const templateFile = fs.readFileSync('./templates/classGenerator.mustache', 'utf-8');

const code = getCode({
  moduleName: 'Test',
  swagger: swaggerFile,
  template: templateFile,
});
```

## Options
`getCode({...})` accepts the following parameters:

```yaml
  moduleName:
    type: string
    required: true
    description: module/class name (used for output file name in CLI)
  template:
    type: object
    required: true
    description: a .mustache template
  swagger:
    type: object
    required: true
    description: swagger object
  outputPath:
    type: string
    required: true
    description: output path for rendered file or application
  imports:
    type: array
    required: false
    description: optional data structure for node.js module imports
  customParams:
    type: object
    required: false
    description: optional variables to add to the object passed to mustache
```

### Template Variables
The following data are passed to the [mustache templates](https://github.com/janl/mustache.js):

```yaml
moduleName:
  type: string
  description: Your AngularJS module name - provided by your options field
imports:
  type: array
  description: optional module imports passed from calling function
constants:
  type: object
  description: optional constants passed from calling function
domain:
  type: string
  description: If all options defined: swagger.schemes[0] + '://' + swagger.host + swagger.basePath
info:
  type: object
securityDefinitions:
  type: array
  items:
    type: object
    properties:
      name:
        type: string
      type:
        type: string
      authorizationUrl:
        type: string
      flow:
        type: string
      scopes:
        type: object
definitions:
  type: array
  items:
    type: object
    properties:
      name:
        type: string
      type:
        type: string
      properties:
        type: object
operations:
  type: array
  items:
    type: object
    properties:
      summary:
        type: string
      description:
        type: string
      operationId:
        type: string
      path:
        type: string
      method:
        type: string
      tags:
        type: array
        items:
          type: string
      consumes:
        type: array
        items:
          type: string
      produces:
        type: array
        items:
          type: string
      parameters:
        type: array
        items:
          type: object
          properties:
            in:
              type: string
            name:
              type: string
            description:
              type: string
            required:
              type: boolean
            schema:
              type: object
                $ref:
                  type: string
        responses:
          type: object
        security:
          type: array
          items:
            type: object
```

#### Custom Mustache Parameters
You can also pass in your own parameters for the mustache templates by adding a `constants` object:

```javascript
var source = codegen.getCode({
    ...
    constants: {
      foo: 'bar',
      app_build_id: env.BUILD_ID,
      app_version: pkg.version
    }
});
```

