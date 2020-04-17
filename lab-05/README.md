<p align="center">
    <img src="../resources/header.png">
</p>

# Laboratorio 5. Cognito

## Introducción

<p align="center">
    <img src="resources/cognito.png"/>
</p>

[AWS Cognito](https://docs.aws.amazon.com/es_es/cognito/?id=docs_gateway) proporciona autenticación, autorización y administración de usuarios para sus aplicaciones y en a nuestros servicios de AWS de la capa de seguridad necesaria para el control del acceso. Podemos usar cognito con diversos proveedores de autenticación como Facebook, Amazon, Google o Apple.

Los dos componentes principales de Amazon Cognito son los grupos de usuarios y los grupos de identidades. 
* **Los grupos de usuarios** son directorios de usuarios que proporcionan a los usuarios de las aplicaciones opciones para inscribirse e iniciar sesión. 
* **Los grupos de identidades** permiten conceder a los usuarios acceso a otros servicios de AWS. Puede utilizar los grupos de identidades y los grupos de usuarios juntos o por separado.

Cognito provee de tres servicios:
-	**Amazon Cognito Federated Identities**. Permiten crear identidades únicas para sus usuarios y federarlas con proveedores de identidad. Con un grupo de identidades, podemos obtener credenciales de AWS temporales con privilegios limitados para obtener acceso a otros servicios de AWS.
-	**Amazon Cognito User Pools**. Directorio de usuarios en Amazon Cognito, permite a los usuarios iniciar sesión en su aplicación web o móvil a través de Amazon Cognito. Los usuarios también pueden iniciar sesión a través de proveedores de identidad sociales como Google, Facebook, Amazon o Apple y a través de proveedores de identidad SAML
-	**Amazon Cognito Sync**. Permite sincronizar los datos de los usuarios, como las preferencias de la aplicación. También amplía estas funcionalidades, ya que permite que diferentes usuarios se sincronicen y colaboren en tiempo real sobre los datos compartidos.

Para nuestra aplicación utilizaremos un **User Pool**:
-	Crearemos un “Cognito User Pool” para el control de acceso de los usuarios finales de nuestra aplicación a una API Gateway, dotando de seguridad a nuestra aplicación.
-	Añadiremos un usuario en el pool para realizar las pruebas de funcionamiento.

## Creación del User Pool
Para crearlo seguiremos los siguientes pasos:
1.	En la consola de AWS, en el menú **Services** buscaremos y seleccionaremos “Cognito”.

<p align="center">
  <img src="resources/img_1.png">
</p>

> :warning: Hay que verificar que te encuentras en la región correcta. Cada uno de los servicios que se creen en los laboratorios (Cognito, API Gateway, Lambda y DynamoDB) deben pertenecer a la misma región. Para tener más información acerca de las regiones puedes acceder a este [enlace](https://docs.aws.amazon.com/es_es/AWSEC2/latest/UserGuide/using-regions-availability-zones.html). Para la elaboración de los laboratorios os sugerimos utilizar Irlanda.

2.	Crearemos un nuevo pool de usuarios pulsando *Manage User Pools* y *Create a user pool*.

<p align="center">
  <img src="resources/img_2.png">
</p>

<p align="center">
  <img src="resources/img_3.png">
</p>

3.	Introduce un nombre para el pool: “EventAppPool_XXXX” (siendo XXXX el identificador único que estes usando) y pulsa *Step through settings*.
4.  En la sección **How do you want your end users to sign in?**, elegimos "Email address or phone number".
5.	En la sección **Which standard attributes do you want to require**, debemos dejar todo vacio. Pulsaremos luego *next step*.
6.	En la siguiente sección **What password strength do you want to require**, estableceremos las limitaciones para las password. 
    Puedes cambiar:
    *	La longitud mínima de la password: la dejamos en 6
    *	Obligatoriedad de caracteres especiales, mayúsculas...: por comodidad, quitamos todas.
El resto de las opciones las dejaremos por defecto y pulsaremos *next step*.
7.	En el menú de la izquierda veremos cada una de las secciones necesarias para establecer las propiedades del pool. Podemos ahora saltar hasta el paso **App clients**, pulsando sobre la sección, dejando las secciones intermedias con los valores por defecto.
<p align="center">
  <img src="resources/img_6.png" width="150px">
</p>

Vamos ahora a crear un [User Pool App Client](https://docs.aws.amazon.com/es_es/cognito/latest/developerguide/user-pool-settings-client-apps.html) dentro de nuestro **User pool**. Esta App Client es una entidad dentro de un user pool que tiene permiso para llamar a API sin autenticar (API que no tengan un usuario autenticado), como las API de registro, inicio de sesión y gestión de contraseñas olvidadas, a las que lógicamente el usuario accede sin haber presentado aún creedenciales.

8. En la sección **App clients** pulsamos *Add an app client* y en el formulario resultante establecemos las propiedades del cliente:
    *	Nombre de la aplicación “EventAppAngular_XXXX” (siendo XXXX el identificador único que estes usando).
    *	Deseleccionamos “Generate client secret”.
Y pulsamos *Create app client* (estos pasos son muy importantes ya que no podrán ser modificados después de crear el app client). Con ello el **app client** habrá sido creado.

9.	En el menú de la izquierda, donde se encuentran las secciones del pool, pulsaremos **Review** para revisar toda las configuraciones establecidas. Y pulsamos *Create pool*. Si todo ha ido bien, debemos navegar a una ventana donde veremos el mensaje “Your user pool was created successfully.”

<p align="center">
  <img src="resources/img_4.png">
</p>

10.	De la ventana donde nos informan que hemos creado el pool, debemos copiar y salvar dos datos muy importantes: el **Pool id** (identificación única para el pool creado) y el **Pool ARN** (el Amazon Resource Name para poder acceder a él), los utilizaremos más tarde en nuestra aplicación:
    * **Pool Id**: eu-central-1_XXXXXXXX
    * **Pool ARN**: arn:aws:cognito-idp:eu-central-1:128434942847:userpool/eu-central-1_XXXXXXX.
11.	En el panel de navegación a la izquierda, dentro de **General Settings**, pulsaremos “App Clients” y salvaremos también el **App client id** de la app client “EventAppAngular”. Este id nos permitirá a nuestro usaurios acceder a los servicios de login sin que se solicite autenticación previa.

## Etiquetando el User Pool

Ahora para facilitar la localización de nuestro User Pool, vamos a etiquetarlo.

En detalle del User Pool creado, en el menú de la izquierda, dentro de general settings está la opción *Tags*, donde puedes añadir dos etiquetas que permitan localizar más fácilmente el pool:
   * **key**: "createdby"   **value**: [vuestro enterprise id]
   * **key**: "training"    **value**: "Openathon IV"


## Habilitar OAuth2.0

Para poder autenticarnos contra cognito desde postman con una interfaz visual, vamos a crear un dominio para cognito, y habilitar otras propiedades:

1. En el menú de la izquierda, pulsamos en **Domain name**
2. Introducimos un prefijo de dominio, por ejemplo, XXXX-events-app, y hacemos click en *Save changes*. La url entera la tenemos que apuntar porque la necesitaremos más adelante.
3. En el menú de la izquierda, pulsamos en *App client settings* en la sección "App Integration".
4. Habilitamos Cognito User Pool.
5. En Callback URL ponemos *myapp://example* (en nuestra caso es irrelevante)
6. En Allowed OAuth Flows, hacemos click en "Implicit grant".
7. En Allowed OAuth Scopes, hacemos click en "openid".
8. Finalmente, hacemos click en *save changes*.

## Creación de un usuario para el User Pool

1.	Dentro de la ventana de administración del user pool que hemos creado (AWS Service **Cognito**, opción “Manage User Pool”, seleccionando el user pool “EventAppPool”), en el panel de navegación a la izquierda, dentro de “General Settings”, pulsaremos *User and Groups* y *create user*.
2.	En la ventana emergente introduciremos el nombre y la contraseña que prefiramos (aunque tendrá que cumplir las condiciones que especificamos en la creación del userpool), desmarcando el envío de invitación y las verificaciones del teléfono y el email. 

<p align="center">
  <img src="resources/img_5.png">
</p>

## Conclusión

Con los pasos realizados en este laboratorio tenemos ya creado un servicio de identidad que permitirá a nuestra aplicación angular disponer de un servicio de login para el acceso a las funciones lambda que publiquemos a través de una API Gateway. 

En el caso que estés usando el usuario de formación para realizar las actividades, considera que el login está utilizando Cognito para autenticarte y que este tiene configurado a Accenture como proveedor de identidad para permitir el uso de sus EID.

[< Lab 04 ](../lab-04)  | [Lab 06 >](../lab-06)

<p align="center">
    <img src="../resources/header.png">
</p>
