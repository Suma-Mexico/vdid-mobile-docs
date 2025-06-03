# Librería vdid_sdk_android - v1.0.0

Repositorio Maven de la librería

<a href="https://central.sonatype.com/artifact/com.sumamexico/vdid_sdk_android/1.0.0" target="_blank"><img src="https://img.shields.io/maven-central/v/com.sumamexico/vdid_sdk_android?versionPrefix=1.0.0&style=plastic&label=vdid_sdk_android&color=3A59D1" alt="VDID_SDK_ANDROID Version" /></a>

## Introducción

Es una librería diseñada para aplicaciones nativas Android que proporciona un entorno completo con pantallas y lógica integrada para realizar una verificación de identidad. Implementado los [componentes de autocaptura de documentos y rostro](/android/LibraryVdidCore.md). Esta solución ha sido desarrollada con el objetivo de facilitar su integración, ofreciendo un ambiente completo para el procesar verificaciones de forma eficiente.

No es un SDK personalizable. Se requiere un token de autorización, generado mediante la API de verificación disponible en [Login - Postman](https://documenter.getpostman.com/view/13807324/UVXgNJ4f#e3cb81e5-ffb1-4bca-8e06-351071e749d8)

> [!NOTE]
>
> El uso de esta librería requiere una licencia válida proporcionada por SUMA México. [Ver detalles](#license)

## 📃 Requerimientos

### Entorno de Desarrollo

Para integrar la librería en una aplicación Android, se deben cumplir los siguientes requisitos:

- **Mínimo nivel de API:** 21 (Android 5.0, Lollipop).
- **Versión recomendada del SDK:** 35.
- **Compatibilidad:** Kotlin 1.9 o superior.
- **Librerias obligatorias:** Jetpack Compose y Hilt

### Dependencias Externas

La librería depende de una librería adicional para la correcta ejecución del proceso de autocaptura. Esta librería debe ser descargada desde un repositorio Maven externo, configurando correctamente el acceso al mismo en el archivo `build.gradle.kts` de la aplicación:

```kotlin
import java.net.URI

dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven {
            url = URI("https://maven.innovatrics.com/releases")
        }
    }
}
```

### Permisos en AndroidManifest.xml

La librería requiere que la aplicación solicite acceso a la cámara para poder capturar imágenes.
Asegúrese de agregar el siguiente permiso en el archivo `AndroidManifest.xml` de la aplicación:

```kotlin
 <uses-permission android:name="android.permission.CAMERA"/>
```

## 🔮 Integración de la librería

### Integración de la librería SDK con Jetpack Compose según versión de Kotlin

Dependiendo de la versión de Kotlin que utilices en tu proyecto, la integración de Jetpack Compose cambia. A continuación se describen las diferencias entre **Kotlin 2.0.0** y **Kotlin 1.9.23**, enfocándose en la configuración necesaria para Compose.

#### 🔷 Kotlin 2.0.0 — Uso del plugin `kotlin-compose`

JetBrains introdujo el plugin `org.jetbrains.kotlin.plugin.compose` (alias `kotlin-compose`) para simplificar la configuración de Compose en Kotlin 2.0.0+.

1. Aplicar los Plugins en `build.gradle.kts` **(raíz del proyecto)**

```kotlin
plugins {
    alias(libs.plugins.android.application) apply false
    alias(libs.plugins.kotlin.android) apply false
    alias(libs.plugins.kotlin.compose) apply false
    alias(libs.plugins.dagger.hilt) apply false
}
```

2. Aplicar los Plugins en `build.gradle.kts` **(módulo de la app)**

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
    alias(libs.plugins.kotlin.compose)
    alias(libs.plugins.dagger.hilt)
    alias(libs.plugins.kotlin.katp)
}
```

3. Integrar las librerias y plugins `libs.versions.toml`

```kotlin
[versions]
kotlin = "2.0.0"
agp = "8.8.2"
vdidSdk = "1.0.0"
activityCompose = "1.10.1"
hilt-android = "2.51.1"

[libraries]
vdid-suma-sdk = { group = "com.sumamexico", name = "vdid_sdk_android", version.ref = "vdidSdk" }
androidx-activity-compose = { group = "androidx.activity", name = "activity-compose", version.ref = "activityCompose" }
hilt-android = { group = "com.google.dagger", name = "hilt-android", version.ref = "hilt-android" }
hilt-compiler = { group = "com.google.dagger", name = "hilt-compiler", version.ref = "hilt-android" }

[plugins]
kotlin-compose = { id = "org.jetbrains.kotlin.plugin.compose", version.ref = "kotlin" }
dagger-hilt = { id = "com.google.dagger.hilt.android", version.ref = "hilt-android" }
kotlin-katp = { id = "kotlin-kapt" }
```

4. Agregar dependencias necesarias en `build.gradle.kts` **(módulo de la app)**

```kotlin
dependencies {
    implementation(libs.vdid.suma.sdk)                // SDK principal
    implementation(libs.androidx.activity.compose)    // Jetpack Compose Activity
    implementation(libs.hilt.android)                 // Hilt DI
    kapt(libs.hilt.compiler)                          // Hilt Annotation Processor
}
```

#### 🔷 Kotlin 1.9.23 — Configuración manual de Compose

En esta versión no existe el plugin `kotlin-compose`, por lo tanto Compose se habilita de forma manual con `buildFeatures` y `composeOptions`.

1. Aplicar los Plugins en `build.gradle.kts` **(módulo de la app)**

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
    alias(libs.plugins.dagger.hilt)
    alias(libs.plugins.kotlin.katp)
}
```

2. Integrar las librerias y plugins `libs.versions.toml`

```kotlin
[versions]
kotlin = "1.9.23"
agp = "8.8.2"
vdidSdk = "1.0.0"
activityCompose = "1.10.1"
hilt-android = "2.51.1"

[libraries]
vdid-suma-sdk = { group = "com.sumamexico", name = "vdid_sdk_android", version.ref = "vdidSdk" }
androidx-activity-compose = { group = "androidx.activity", name = "activity-compose", version.ref = "activityCompose" }
hilt-android = { group = "com.google.dagger", name = "hilt-android", version.ref = "hilt-android" }
hilt-compiler = { group = "com.google.dagger", name = "hilt-compiler", version.ref = "hilt-android" }

[plugins]
dagger-hilt = { id = "com.google.dagger.hilt.android", version.ref = "hilt-android" }
kotlin-katp = { id = "kotlin-kapt" }
```

3. Agregar dependencias necesarias en `build.gradle.kts` (módulo de la app)

```kotlin
dependencies {
    implementation(libs.vdid.suma.sdk)                // SDK principal
    implementation(libs.androidx.activity.compose)    // Jetpack Compose Activity
    implementation(libs.hilt.android)                 // Hilt DI
    kapt(libs.hilt.compiler)                          // Hilt Annotation Processor
}
```

4. Configuración manual del compose en `build.gradle.kts` **(módulo de la app)**

```kotlin
android {
    namespace = "com.example.sample_sdk_android"
    compileSdk = 35

    defaultConfig {
        applicationId = "com.example.sample_sdk_android"
        minSdk = 21
        targetSdk = 35
        versionCode = 1
        versionName = "1.0"
        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }

    composeOptions {
        kotlinCompilerExtensionVersion = "1.5.11"
    }
    buildFeatures {
        compose = true
    }
   ...
}
```

### Implementación de la librería SDK

- Crear clase `Application` con Hilt

```kotlin
// MainApp.kt
@HiltAndroidApp
class MainApp : Application()
```

- Asegúrate de registrarla en el `AndroidManifest.xml`:

```kotlin
<application
    android:name=".MainApp"
    ... >
```

- Implementar la lógica de inicio en `MainActivity`

```kotlin
@AndroidEntryPoint
class MainActivity : ComponentActivity() {
    @RequiresApi(Build.VERSION_CODES.TIRAMISU)
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            val intent = Intent(this, VdidSdkActivity::class.java).apply {
                putExtra("userId", "TU_USER_ID")
                putExtra("tokenJwt", "TOKEN_JWT_GENERADO")
            }
            startForResult.launch(intent)
        }
    }

    private val startForResult =
        registerForActivityResult(ActivityResultContracts.StartActivityForResult()) { result ->
            if (result.resultCode == RESULT_OK) {
                val data: Intent? = result.data
                val verificationResult = data?.getStringExtra(VdidSdkActivity.RESULT_EXTRA)
                Toast.makeText(this, "Resultado: $verificationResult", Toast.LENGTH_LONG).show()
            }
        }
}
```

## ⚙ Funcionalidades de la Librería

**1. Proceso de Verificación**

La librería inicia un flujo completo de autocaptura y verificación que incluye:

- Captura automática de rostro
- Captura automática de documento (INE/Pasaporte)
- Validación de imágenes
- Envío y procesamiento seguro

**2. Parámetros Requeridos**

Al iniciar la actividad VdidSdkActivity, se deben enviar los siguientes extras:

| Clave    | Tipo   | Descripción                                    |
| -------- | ------ | ---------------------------------------------- |
| userId   | String | Identificador del usuario/cliente              |
| tokenJwt | String | Token de autorización generado vía API externa |

> [!WARNING]
>
> Ambos parámetros son obligatorios. Si faltan, el SDK no podrá terminar el flujo correctamente.

**3. Parámetros Requeridos**

Una vez finalizado el proceso, el resultado se entrega por medio del ActivityResult. Se accede al valor con:

```kotlin
  val verificationResult = data?.getStringExtra(VdidSdkActivity.RESULT_EXTRA)
```

El valor devuelto puede usarse para:

- Mostrar mensajes de éxito/error
- Redirigir al usuario
- Guardar el resultado en backend

## <a id="license"></a>📜 Licencia

Para utilizar la librería `VDID_SDK_ANDROID`, debe contactar al equipo de soporte de **SUMA México** y proporcionar el `applicationId` de su aplicación. La licencia que se le entregará estará vinculada exclusivamente a este identificador único.

Este valor puede obtenerse de la siguiente forma:

- Desde el archivo `build.gradle`, en la sección `android {}`:

  ```kotlin
  android {
      defaultConfig {
          applicationId "com.ejemplo.app"
          ...
      }
  }
  ```

  > [!NOTE]
  >
  > La licencia será válida únicamente para el `applicationId` registrado. Si cambia este valor, deberá solicitar una nueva licencia.

### Entrega y Uso de la Licencia

Una vez que el cliente proporciona su applicationId, se le entregará un archivo de licencia con las siguientes características:

- **Nombre del archivo:** dot_license.lic
- **Ubicación requerida:** res/raw
- **Restricciones:**
  - El cliente NO debe modificar el nombre del archivo.
  - El contenido del archivo NO debe ser alterado en ningún caso.
  - No se entregarán licencias de prueba. La licencia proporcionada será la definitiva para toda la integración.

### Alojamiento de la licencia

El archivo debe ser colocado dentro del directorio de recursos `res/raw/` en la estructura del proyecto de la aplicación:

```
app/
└── src/
    └── main/
        └── res/
            └── raw/
                └── dot_license.lic
```

> [!NOTE]
>
> Si la licencia no está correctamente colocada en esta ubicación, la librería **NO funcionará**.

## 🚨 Consideraciones adicionales

- El SDK no es personalizable visualmente.
- Es obligatorio integrar Jetpack Compose y Hilt.
- El `tokenJwt` debe generarse a través de la API documentada en [Postman](https://documenter.getpostman.com/view/13807324/UVXgNJ4f#e3cb81e5-ffb1-4bca-8e06-351071e749d8).
- No es necesario crear pantallas propias. El SDK ya contiene toda la experiencia integrada.
