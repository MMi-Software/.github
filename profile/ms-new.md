## Paso 1: Crear el Repositorio en Amazon ECR

1. **Crear el Repositorio en ECR:**
   - Ve a la consola de Amazon ECR.
   - Selecciona "Create repository".
   - Configura el nombre del repositorio usando el prefijo `ms-` seguido del nombre identificatorio. Ejemplo: `ms-reports`.
   - Selecciona `Private` como visibilidad.

## Paso 2: Configurar el Dockerfile y Crear la Imagen

1. **Configurar el Dockerfile:**
   - Asegúrate de tener un archivo `Dockerfile` configurado en tu proyecto de NestJS. Un ejemplo básico podría ser:
   ```dockerfile
   # Base image
   FROM node:16-alpine

   # Set working directory
   WORKDIR /app

   # Install dependencies
   COPY package*.json ./
   RUN npm install --production

   # Copy the rest of the application code
   COPY . .

   # Expose the application port
   EXPOSE 3000

   # Start the application
   CMD ["npm", "run", "start:prod"]
   ```

2. **Construir y Subir la Imagen al ECR:**
   - Desde tu terminal, ejecuta los siguientes comandos:
   ```bash
   # Construir la imagen Docker
   docker build -t ms-reports .

   # Taggear la imagen con el nombre del repositorio en ECR
   docker tag ms-reports:latest <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/ms-reports:latest

   # Log in al ECR usando el perfil `msuite-dev`
   aws ecr get-login-password --profile msuite-dev --region us-east-1 | docker login --username AWS --password-stdin <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com

   # Subir la imagen a ECR
   docker push <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/ms-reports:latest
   ```

## Paso 3: Crear el App Runner en AWS

1. **Configurar el App Runner:**
   - Ve a la consola de AWS App Runner.
   - Selecciona "Create an App Runner service".
   - **Origen:**
     - Tipo de repositorio: Registro de contenedor.
     - Proveedor: Amazon ECR.
     - URI de imagen de contenedor: Selecciona el ECR creado anteriormente (`ms-reports`).
   - **Configuración de implementación:**
     - Desencadenador de implementación: Automático.
     - Roles de acceso: Selecciona o crea `AppRunnerAccessRole`.
   - **Configuración de servicio:**
     - Nombre del servicio: Usa el mismo nombre que el ECR (`ms-reports`).
     - Variables de entorno: Configura las variables necesarias para tu aplicación.

## Paso 4: Configurar GitHub Actions para Despliegue Automático

1. **Crear y Configurar el Archivo de Workflow:**
   - En la raíz de tu repositorio en GitHub, crea un archivo en `.github/workflows/deploy.yml` con el siguiente contenido:
   ```yaml
   name: Deploy to AWS App Runner

   on:
     workflow_dispatch:  # Esto permite el despliegue manual
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
       - name: Check out the repository
         uses: actions/checkout@v3

       - name: Log in to Amazon ECR
         env:
           AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID_DEV }}
           AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY_DEV }}
           AWS_REGION: ${{ secrets.AWS_REGION_DEV }}
           AWS_ACCOUNT_ID: ${{ secrets.AWS_ACCOUNT_ID_DEV }}
         run: |
             aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com

       - name: Build and tag Docker image
         env: 
           REPOSITORY_NAME: ms-reports
         run: |
             docker build -t $REPOSITORY_NAME:latest .

       - name: Push Docker image to ECR
         env: 
           REPOSITORY_NAME: ms-reports
           AWS_ACCOUNT_ID: ${{ secrets.AWS_ACCOUNT_ID_DEV }}
           AWS_REGION: ${{ secrets.AWS_REGION_DEV }}
         run: |
             docker tag $REPOSITORY_NAME:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$REPOSITORY_NAME:latest
             docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$REPOSITORY_NAME:latest

       - name: Notify success
         run: echo "Deployment to AWS App Runner was successful!"
   ```

## Paso 5: Subir el Repositorio a GitHub y Configurar Secretos

1. **Subir el Repositorio a GitHub:**
   - Asegúrate de que todo tu código esté en el repositorio de GitHub.

2. **Configurar Secretos:**
   - Ve a la sección de "Settings" de tu repositorio en GitHub.
   - Añade los siguientes secretos:
     - `AWS_ACCESS_KEY_ID_DEV`
     - `AWS_SECRET_ACCESS_KEY_DEV`
     - `AWS_REGION_DEV`
     - `AWS_ACCOUNT_ID_DEV`

Con estos pasos, deberías tener todo configurado para desplegar tu microservicio desde local a AWS utilizando GitHub Actions.
