# Librería vdid_core - v1.0.0

📌[Repositorio Maven de la librería](https://central.sonatype.com/artifact/com.sumamexico/vdid_core/1.0.0)

## Introducción

![Maven Central Version](https://img.shields.io/maven-central/v/com.sumamexico/vdid_core?versionPrefix=1.0.0&style=plastic&color=3A59D1)

Es una librería diseñada para aplicaciones nativas Android que permite integrar componentes de autocaptura de documentos y rostro. Esta solución ha sido desarrollada para facilitar su integración, ofreciendo funciones clave para gestionar el flujo de captura y procesamiento de imágenes de forma eficiente.

La librería se basa en un fragmento especializado que se controla mediante diversas funciones, permitiendo iniciar, detener y manejar la captura, así como convertir las imágenes obtenidas al formato Base64 para su posterior uso.

> [!NOTE]
>
> El uso de estos componentes requiere una licencia válida proporcionada por SUMA México. [Ver detalles](#license)

## 📃 Requerimientos

### Entorno de Desarrollo

Para integrar la librería en una aplicación Android, se deben cumplir los siguientes requisitos:

- **Mínimo nivel de API:** 21 (Android 5.0, Lollipop).
- **Versión recomendada del SDK:** 35 (para compatibilidad total con las funciones expuestas).
- **Compatibilidad:** Kotlin 1.8 o superior y Java 8.

### Dependencias Externas

La librería depende de una librería adicional para la correcta ejecución del proceso de autocaptura. Esta librería debe ser descargada desde un repositorio Maven externo, configurando correctamente el acceso al mismo en el archivo build.gradle.kts de la aplicación:

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

La librería proporciona un fragmento independiente que puede integrarse en una actividad para gestionar la autocaptura de documentos o rostro, según la funcionalidad deseada.

### Autocaptura de Documentos

#### Incorporación del Fragmento en el Layout

Primero, asegúrese de tener un contenedor para alojar el fragmento dentro del layout de su actividad (por ejemplo, `activity_main.xml`):

```xml

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/fragmentContainer"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

#### Inicialización del Fragmento en la Actividad

Luego, dentro de tu `Activity`, debes implementar la interfaz `OnCaptureListener` para recibir los resultados de la captura. El fragmento se obtiene desde la clase `DocumentLibrary` y se agrega dinámicamente al contenedor.

```kotlin

import com.sumamexico.vdid_core.document.DocumentLibrary
import com.sumamexico.vdid_core.OnCaptureListener
import com.sumamexico.vdid_core.CaptureUtils

class MainActivity : AppCompatActivity(), OnCaptureListener {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // Inicializa el fragmento de autocaptura y lo agrega al layout
        val myFragment = DocumentLibrary.autocaptureDocumentFragment(this)
        supportFragmentManager.commit {
            replace(R.id.fragmentContainer, myFragment)
        }
    }

    // Callback si la captura falla
    override fun onFailed(message: String) {
        Toast.makeText(this, message, Toast.LENGTH_SHORT).show()
    }

    // Callback si la captura es exitosa
    override fun onCaptured(image: ByteArray) {
        val imgBase64 = CaptureUtils.imageToBase64(image)
        println("##### Imagen capturada en base64: $imgBase64")
    }
}

```

### Autocaptura del Rostro

#### Incorporación del Fragmento en el Layout

Primero, asegúrese de tener un contenedor para alojar el fragmento dentro del layout de su actividad (por ejemplo, `activity_main.xml`):

```xml

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/fragmentFaceContainer"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

#### Inicialización del Fragmento en la Actividad

Luego, dentro de tu `Activity`, debes implementar la interfaz `OnCaptureListener` para recibir los resultados de la captura. El fragmento se obtiene desde la clase `FaceLibrary` y se agrega dinámicamente al contenedor.

```kotlin

import com.sumamexico.vdid_core.face.FaceLibrary
import com.sumamexico.vdid_core.OnCaptureListener
import com.sumamexico.vdid_core.CaptureUtils

class MainActivity : AppCompatActivity(), OnCaptureListener {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // Inicializa el fragmento de autocaptura y lo agrega al layout
        val myFragment = FaceLibrary.autocaptureDocumentFragment(this)
        supportFragmentManager.commit {
            replace(R.id.fragmentContainer, myFragment)
        }
    }

    // Callback si la captura falla
    override fun onFailed(message: String) {
        Toast.makeText(this, message, Toast.LENGTH_SHORT).show()
    }

    // Callback si la captura es exitosa
    override fun onCaptured(image: ByteArray) {
        val imgBase64 = CaptureUtils.imageToBase64(image)
        println("##### Imagen capturada en base64: $imgBase64")
    }
}

```

> 📌 Notas adicionales:
>
> - Asegúrate de tener los permisos necesarios (cámara) gestionados antes de iniciar el fragmento.
> - La librería maneja internamente la lógica de autocaptura y prueba de vida, devolviendo la imagen en formato ByteArray.

## ⚙ Funcionalidades de la Librería

Las siguientes funciones están disponibles en ambos componentes (documento y rostro) y conservan el mismo comportamiento en cada caso.

### Continuar detección

Esta función permite reanudar el proceso de autocaptura. Es útil en escenarios donde es necesario capturar más de una imagen de un documento.

```kotlin
DocumentLibrary.continueDocumentDetection()
```

```kotlin
FaceLibrary.continueFaceDetection()
```

### Detener detección

Esta función permite detener el proceso de autocaptura de manera asíncrona. Es útil cuando se desea interrumpir la captura de documentos antes de completarla, por ejemplo, cuando el usuario desea cancelar el proceso.

Para volver a reiniciar el proceso de captura se debe hacer uso de cualquiera de las funciones que permiten reanudar el proceso de autocaptura, descritas anteriormente.

```kotlin
DocumentLibrary.stopDocumentDetection { message ->
                Toast.makeText(
                    this, message, Toast.LENGTH_SHORT).show()
            }
```

```kotlin
FaceLibrary.stopDocumentDetection { message ->
                Toast.makeText(
                    this, message, Toast.LENGTH_SHORT
                ).show()
            }
```

### Obtener el identificador de la aplicación

Esta función es fundamental para obtener el ID de la aplicación en tiempo de ejecución. El ID de la aplicación es utilizado para [la validación y generación de licencias](#license). Si la aplicación utiliza diferentes flavors con distintos IDs, cada uno debe tener su propia licencia. La licencia debe ser colocada en la carpeta res/raw.

```kotlin
CaptureUtils.getApplicationId(this)
```

### Convertir imagenes a base64

Esta función convierte la imagen capturada en el proceso de autocaptura, de un array de bytes a formato Base64. Esto es útil cuando se necesita transmitir o almacenar las imágenes en un formato compatible con nuestras Apis que les proporcionamos para el procesamiento de verificación de documentos.

```kotlin
CaptureUtils.imageToBase64(imageByteArray)
```

### Liberar recursos

Esta función libera los recursos utilizados por la librería. Es importante utilizarla solo cuando el proceso de captura haya finalizado o cuando la aplicación ya no necesite realizar más capturas, ya que liberar los recursos en medio del proceso causa errores.

```kotlin
CaptureUtils.cleanupResources()
```

### Estado de los componentes

Esta función devuelve un valor booleano que indica si la librería está correctamente inicializada y lista para funcionar. Esto es útil para verificar que el proceso de captura está activo antes de intentar utilizar otras funciones.

```kotlin
CaptureUtils.stateDetection()
```

> [!WARNING]
>
> Es crucial liberar los recursos solo cuando el proceso de captura haya terminado, para evitar fallos inesperados en el proceso de detección. Si se llama a `cleanupResources()` durante la captura, los recursos necesarios para el proceso serán eliminados, causando errores.

## <a id="license"></a>📜 Licencia

Para utilizar la librería `VDID_CORE`, debe contactar al equipo de soporte de **SUMA México** y proporcionar el `applicationId` de su aplicación. La licencia que se le entregará estará vinculada exclusivamente a este identificador único.

Este valor puede obtenerse de dos maneras:

1. En tiempo de ejecución, utilizando la función proporcionada por la librería:

```kotlin
val getId = CaptureUtils.getApplicationId(this)
```

Esta función retorna el `applicationId` definido en el archivo `build.gradle` de su módulo (`applicationId "com.ejemplo.app"`). Asegúrese de que este valor coincida exactamente con el que se registre para la licencia.

2. Desde el archivo `build.gradle`, en la sección `android {}`:

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
> La licencia será válida únicamente para el applicationId registrado. Si cambia este valor, deberá solicitar una nueva licencia.

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
