### Creación/Migración de repositorios

Pasos para estructurar la actualización de una Lambda en AWS de manera manual utilizando una rama específica del repositorio utilizando GitHub Actions.

1. **Configurar el repositorio de GitHub**:
   - Crea un repositorio en GitHub utilizando el prefijo `lambda-`. Ejemplo: `lambda-mSand-ingesta`.
   - Push del repositorio local en el repositorio remoto.

2. **Configurar la Lambda en AWS**:
   - Crea una función Lambda en AWS.
   - La función Lambda debe estar configurada para ser actualizada mediante la carga de un nuevo paquete (ZIP).

3. **Configurar GitHub Actions**:
   - En el repositorio remoto, crear un archivo de flujo de trabajo (`.github/workflows/deploy.yml`).
   - Este flujo de trabajo se disparará desde la consola de actions del repositorio

   Ejemplo del job de dev, se debe configurar para cada uno de los ambientes:
```yaml
name: Deploy Lambda

on:
  push:
    branches:
      - dev
      - qa
  pull_request:
    branches:
      - main
  workflow_dispatch: # Permite el disparo manual del workflow
    inputs:
      environment:
        description: 'Choose the environment to deploy'
        required: true
        default: 'dev'
        type: choice
        options:
          - dev
          - qa
          - prod

jobs:
  deploy-dev:
    if: github.ref == 'refs/heads/dev' || github.event_name == 'workflow_dispatch' && github.event.inputs.environment == 'dev'
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '18'
      - name: Install dependencies
        run: npm install
      - name: Zip Lambda Function
        run: zip -r lambda.zip .
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2 #configurar las credenciales de AWS necesarias para interactuar con los servicios de AWS.
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID_DEV }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY_DEV }}
          region: us-east-1
      - name: Deploy to AWS Lambda (Dev) #actualizar el código de una función Lambda con el archivo ZIP generado.
        run: |
          aws lambda update-function-code \
            --function-name tu-nombre-de-lambda-dev \
            --zip-file fileb://lambda.zip
```

4. **Configurar las credenciales en GitHub**:
   - Ve a los ajustes de tu repositorio en GitHub y añade las credenciales de AWS (Access Key ID y Secret Access Key) en la sección de "Secrets". Esto se hace en "Settings" > "Secrets and variables" > "Actions".
   - Crea dos secretos: `AWS_ACCESS_KEY_ID` y `AWS_SECRET_ACCESS_KEY`, con las credenciales correspondientes, teniendo en cuenta que las variables finalizan con el ambiente: `DEV`, `QA` o `PROD`

5. **Probar la integración**:
   - Realiza una modificación en el código. Push a la rama especificada.
   - Utiliza la consola de los Actions para disparar el deploy.
