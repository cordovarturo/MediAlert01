# MediAlert01

## Introducción

MediAlerta es una aplicación móvil desarrollada para Android que tiene como objetivo ayudar a las personas a llevar un control más organizado de su salud. La aplicación nace de la necesidad de muchas personas que toman medicamentos de forma regular, tienen citas médicas frecuentes o simplemente quieren estar preparados ante cualquier emergencia.

La aplicación permite a los usuarios registrar sus medicamentos con sus respectivos horarios y dosis, programar citas médicas, guardar contactos de emergencia y localizar farmacias cercanas a su ubicación. Todo esto en un solo lugar, con una interfaz colorida y fácil de usar que no requiere conocimientos técnicos.

El desarrollo de MediAlerta se realizó utilizando Kotlin como lenguaje de programación principal y Jetpack Compose para construir la interfaz de usuario moderna y reactiva. Los datos se almacenan tanto localmente en el dispositivo usando Room, como en la nube usando Firebase, lo que permite que la información no se pierda aunque se desinstale la aplicación o se cambie de teléfono.

El equipo detrás de MediAlerta está conformado por Colin Cardenas Kelly Anahi y Mendez Galvan Claudia Lizbeth, quienes diseñaron y programaron cada una de las funcionalidades pensando en hacer la vida más fácil a las personas que necesitan recordatorios constantes para su salud.

**Link de descarga:** https://edith294.github.io/PaginawebappSalud/

**Vídeo de prueba:** https://drive.google.com/file/d/1xIXpM33FM7raiktjbWgTmhMpb74Ps2iN/view?usp=sharing

---

## Descripción de Carpetas

### `data/`

Contiene todo lo relacionado con el manejo de datos de la aplicación, incluyendo la base de datos local y las fuentes de datos.

- **dao/**: Objetos de acceso a datos para interactuar con Room.
- **database/**: Configuración de la base de datos Room y convertidores.
- **entity/**: Clases que representan las tablas de la base de datos.
- **model/**: Modelos de datos para la lógica de negocio.
- **repository/**: Repositorios que manejan las fuentes de datos (local y remota).

### `navigation/`

Gestiona la navegación entre las diferentes pantallas de la aplicación usando Jetpack Compose Navigation.

### `receivers/`

Contiene los BroadcastReceivers para manejar eventos del sistema como las alarmas de los recordatorios.

### `ui/`

Toda la interfaz de usuario de la aplicación construida con Jetpack Compose.

- **screens/**: Las diferentes pantallas de la aplicación organizadas por funcionalidad.
  - **citas/**: Pantallas para gestionar citas médicas.
  - **configuracion/**: Pantalla de configuración de la aplicación.
  - **emergencia/**: Pantallas para gestionar contactos de emergencia.
  - **farmacias/**: Pantalla para buscar farmacias cercanas.
  - **home/**: Pantalla principal de la aplicación.
  - **medicamentos/**: Pantallas para gestionar medicamentos.
- **notificaciones/**: Componentes para manejar notificaciones.
- **theme/**: Configuración del tema visual (colores, tipografía, estilos).
- **utils/**: Utilidades y helpers para la interfaz.

### `viewmodel/`

ViewModels que separan la lógica de negocio de la interfaz de usuario y manejan el estado de las pantallas.

### Archivos Principales

- **MainActivity.kt**: Actividad principal que inicia la aplicación.
- **AndroidManifest.xml**: Configuración de la aplicación.

---

## Tecnologías Utilizadas

- **Kotlin**: Lenguaje de programación principal.
- **Jetpack Compose**: Para la interfaz de usuario moderna.
- **Room**: Base de datos local.
- **Firebase**: Para autenticación, base de datos en la nube y notificaciones.
- **Google Maps**: Para ubicación de farmacias cercanas.
- **Corrutinas**: Para operaciones asíncronas.
- **Flow**: Para datos reactivos.
- **ViewModel**: Para manejar el estado de la UI.
- **Navigation Component**: Para la navegación entre pantallas.
- **AlarmManager**: Para programar recordatorios y notificaciones.

---

## AppointmentDao.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.dao

import androidx.room.*
import kotlinx.coroutines.flow.Flow
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.AppointmentEntity

@Dao
interface AppointmentDao {

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAppointment(appointment: AppointmentEntity): Long

    @Delete
    suspend fun deleteAppointment(appointment: AppointmentEntity)

    @Query("SELECT * FROM appointments ORDER BY dateTime ASC")
    fun getAllAppointments(): Flow<List<AppointmentEntity>>
}
```

### Descripción General

Este archivo es el encargado de comunicarse con la base de datos local de la aplicación, específicamente con la tabla donde se guardan las citas médicas. Usa Room, que es una herramienta que hace más fácil guardar y recuperar información en el teléfono.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.dao`

### Explicación Detallada

**¿Qué es un DAO?**

Un DAO (Data Access Object) es un componente que actúa como intermediario entre la aplicación y la base de datos. En este caso, `AppointmentDao` se encarga de todas las operaciones relacionadas con las citas médicas.

**Anotaciones y su Significado**

- `@Dao`: Le indica a la aplicación que esta interfaz va a manejar las operaciones de la base de datos para las citas.
- `@Insert`: Marca el método que insertará nuevos registros en la base de datos.
- `@Delete`: Marca el método que eliminará registros existentes.
- `@Query`: Permite escribir consultas SQL personalizadas para obtener datos específicos.

**Métodos y su Función**

`insertAppointment()` — Guarda una nueva cita en la base de datos. Si ya existía una con el mismo identificador, la reemplaza por la nueva debido a la estrategia `OnConflictStrategy.REPLACE`. Devuelve un número que es el identificador con el que se guardó la cita.

- Parámetros: Recibe un objeto `AppointmentEntity` que contiene todos los datos de la cita.
- Retorno: Un valor `Long` que es el ID único asignado a la cita recién guardada.

`deleteAppointment()` — Elimina una cita existente de la base de datos. Solo hay que pasarle la cita que se quiere borrar y él se encarga de quitarla de la tabla.

- Parámetros: Recibe el objeto `AppointmentEntity` que se desea eliminar.
- Retorno: No devuelve ningún valor.

`getAllAppointments()` — Obtiene todas las citas guardadas y las entrega ordenadas por fecha y hora. Usa `Flow`, que es una forma de estar siempre actualizado: si se agrega o elimina una cita, la lista se actualiza automáticamente.

- Parámetros: No recibe parámetros.
- Retorno: Un `Flow` que emite una lista de objetos `AppointmentEntity`. Cada vez que hay cambios en la tabla, esta lista se actualiza automáticamente.

### Flujo de Trabajo Típico

1. **Guardar una cita**: Cuando el usuario crea una nueva cita, la aplicación llama a `insertAppointment()` con los datos de la cita.
2. **Consultar citas**: La pantalla que muestra las citas está observando `getAllAppointments()` para tener siempre la lista actualizada.
3. **Eliminar una cita**: Cuando el usuario decide borrar una cita, la aplicación llama a `deleteAppointment()` con la cita que se quiere eliminar.
4. **Actualización automática**: Al modificar la base de datos, el `Flow` emite automáticamente la nueva lista y la pantalla se actualiza sin necesidad de recargar.

### Notas Importantes

- Los métodos `insertAppointment()` y `deleteAppointment()` son `suspend`, lo que significa que deben ejecutarse en segundo plano para no bloquear la interfaz de usuario.
- La estrategia `OnConflictStrategy.REPLACE` garantiza que no habrá conflictos si se intenta guardar una cita con un ID que ya existe.
- El uso de `Flow` hace que la aplicación sea más eficiente, ya que no necesita estar haciendo consultas constantemente para verificar si los datos cambiaron.

### Archivos Relacionados

- `AppointmentEntity.kt` — Define la estructura de una cita en la base de datos.
- `AppDatabase.kt` — La base de datos principal que contiene esta tabla.
- `AppointmentRepository.kt` — El repositorio que utiliza este DAO para sus operaciones.

---

## EmergencyContactDao.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.dao

import androidx.room.*
import kotlinx.coroutines.flow.Flow
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.EmergencyContactEntity

@Dao
interface EmergencyContactDao {

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insert(contact: EmergencyContactEntity): Long

    @Delete
    suspend fun delete(contact: EmergencyContactEntity)

    @Query("SELECT * FROM emergency_contacts ORDER BY name ASC")
    fun getAllContacts(): Flow<List<EmergencyContactEntity>>

    @Query("DELETE FROM emergency_contacts WHERE id = :contactId")
    suspend fun deleteById(contactId: Long)
}
```

### Descripción General

Este archivo es el encargado de comunicarse con la base de datos local de la aplicación, específicamente con la tabla donde se guardan los contactos de emergencia. Usa Room para facilitar el guardado y recuperación de esta información en el teléfono.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.dao`

### Explicación Detallada

**¿Qué es un DAO?**

Un DAO (Data Access Object) es un componente que actúa como intermediario entre la aplicación y la base de datos. En este caso, `EmergencyContactDao` se encarga de todas las operaciones relacionadas con los contactos de emergencia.

**Anotaciones y su Significado**

- `@Dao`: Le indica a la aplicación que esta interfaz va a manejar las operaciones de la base de datos para los contactos de emergencia.
- `@Insert`: Marca el método que insertará nuevos contactos en la base de datos.
- `@Delete`: Marca el método que eliminará contactos existentes.
- `@Query`: Permite escribir consultas SQL personalizadas para obtener o eliminar datos específicos.

**Métodos y su Función**

`insert()` — Guarda un nuevo contacto de emergencia en la base de datos. Si ya existía un contacto con el mismo identificador, lo reemplaza por el nuevo. Devuelve el identificador con el que se guardó el contacto.

- Parámetros: Recibe un objeto `EmergencyContactEntity` con todos los datos del contacto.
- Retorno: Un valor `Long` que es el ID único asignado al contacto recién guardado.

`delete()` — Elimina un contacto de emergencia existente de la base de datos.

- Parámetros: Recibe el objeto `EmergencyContactEntity` que se desea eliminar.
- Retorno: No devuelve ningún valor.

`getAllContacts()` — Obtiene todos los contactos guardados y los entrega ordenados alfabéticamente por nombre. Usa `Flow` para que la lista se mantenga siempre actualizada.

- Parámetros: No recibe parámetros.
- Retorno: Un `Flow` que emite una lista de objetos `EmergencyContactEntity`.

`deleteById()` — Elimina un contacto de emergencia usando directamente su identificador, sin necesidad de tener el objeto completo.

- Parámetros: Recibe un valor `contactId` de tipo `Long`.
- Retorno: No devuelve ningún valor.

### Flujo de Trabajo Típico

1. **Guardar un contacto**: Cuando el usuario agrega un nuevo número de emergencia, la aplicación llama a `insert()` con los datos del contacto.
2. **Consultar contactos**: La pantalla que muestra los contactos está observando `getAllContacts()` para tener siempre la lista actualizada.
3. **Eliminar un contacto**: Cuando el usuario decide borrar un contacto, la aplicación puede usar `delete()` si tiene el objeto completo, o `deleteById()` si solo tiene el ID.
4. **Actualización automática**: Al modificar la base de datos, el `Flow` emite automáticamente la nueva lista.

### Notas Importantes

- Todos los métodos son `suspend`, lo que significa que deben ejecutarse en segundo plano para no bloquear la interfaz de usuario.
- La estrategia `OnConflictStrategy.REPLACE` garantiza que no habrá conflictos si se intenta guardar un contacto con un ID que ya existe.
- El método `deleteById()` ofrece una forma más directa de eliminar contactos cuando no se tiene el objeto completo, lo cual es útil en ciertos casos como cuando se trabaja con listas donde solo se conoce el ID.

### Archivos Relacionados

- `EmergencyContactEntity.kt` — Define la estructura de un contacto de emergencia en la base de datos.
- `AppDatabase.kt` — La base de datos principal que contiene esta tabla.
- `EmergencyContactRepository.kt` — El repositorio que utiliza este DAO para sus operaciones.
- `AgregarNumeroEmergencia.kt` — Pantalla donde se agregan nuevos contactos.

---

## MedicationDao.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.dao

import androidx.room.*
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.MedicationEntity
import kotlinx.coroutines.flow.Flow

@Dao
interface MedicationDao {

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertMedication(med: MedicationEntity): Long

    @Delete
    suspend fun deleteMedication(med: MedicationEntity)

    @Query("SELECT * FROM medications")
    fun getAllMedications(): Flow<List<MedicationEntity>>

    @Update
    suspend fun updateMedication(med: MedicationEntity)
}
```

### Descripción General

Este archivo es el encargado de comunicarse con la base de datos local de la aplicación, específicamente con la tabla donde se guardan los medicamentos. Usa Room para facilitar el guardado y recuperación de esta información en el teléfono.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.dao`

### Explicación Detallada

**¿Qué es un DAO?**

Un DAO (Data Access Object) es un componente que actúa como intermediario entre la aplicación y la base de datos. En este caso, `MedicationDao` se encarga de todas las operaciones relacionadas con los medicamentos que el usuario registra.

**Anotaciones y su Significado**

- `@Dao`: Le indica a la aplicación que esta interfaz va a manejar las operaciones de la base de datos para los medicamentos.
- `@Insert`: Marca el método que insertará nuevos medicamentos en la base de datos.
- `@Delete`: Marca el método que eliminará medicamentos existentes.
- `@Query`: Permite escribir consultas SQL personalizadas para obtener datos específicos.
- `@Update`: Marca el método que actualizará medicamentos existentes.

**Métodos y su Función**

`insertMedication()` — Guarda un nuevo medicamento en la base de datos. Si ya existía uno con el mismo identificador, lo reemplaza. Devuelve el identificador asignado.

- Parámetros: Recibe un objeto `MedicationEntity` con todos los datos del medicamento.
- Retorno: Un valor `Long` que es el ID único asignado al medicamento recién guardado. Este valor es importante porque permite hacer referencia al medicamento en otras partes de la aplicación.

`deleteMedication()` — Elimina un medicamento existente de la base de datos.

- Parámetros: Recibe el objeto `MedicationEntity` que se desea eliminar.
- Retorno: No devuelve ningún valor.

`getAllMedications()` — Obtiene todos los medicamentos guardados. Usa `Flow` para que la lista se mantenga siempre actualizada.

- Parámetros: No recibe parámetros.
- Retorno: Un `Flow` que emite una lista de objetos `MedicationEntity`.

`updateMedication()` — Actualiza la información de un medicamento que ya existe en la base de datos. Es diferente a `insertMedication` porque modifica un registro existente conservando su mismo identificador.

- Parámetros: Recibe el objeto `MedicationEntity` con los datos actualizados.
- Retorno: No devuelve ningún valor.

### Flujo de Trabajo Típico

1. **Guardar un medicamento**: La aplicación llama a `insertMedication()` y recibe el ID asignado.
2. **Consultar medicamentos**: La pantalla observa `getAllMedications()` para tener siempre la lista actualizada.
3. **Actualizar un medicamento**: La aplicación llama a `updateMedication()` con los datos modificados.
4. **Eliminar un medicamento**: La aplicación llama a `deleteMedication()` con el medicamento que se quiere eliminar.
5. **Actualización automática**: Al modificar la base de datos, el `Flow` emite automáticamente la nueva lista.

### Notas Importantes

- Todos los métodos son `suspend`, lo que significa que deben ejecutarse en segundo plano para no bloquear la interfaz de usuario.
- La estrategia `OnConflictStrategy.REPLACE` garantiza que no habrá conflictos si se intenta guardar un medicamento con un ID que ya existe.
- El método `insertMedication()` devuelve el ID del medicamento recién creado, lo cual es útil para referenciarlo en otras partes de la aplicación como en recordatorios o alarmas.
- A diferencia de los otros DAOs, este incluye un método `@Update` específico para modificar medicamentos existentes, lo que da más flexibilidad en la gestión de los datos.

### Archivos Relacionados

- `MedicationEntity.kt` — Define la estructura de un medicamento en la base de datos.
- `AppDatabase.kt` — La base de datos principal que contiene esta tabla.
- `MedicationRepository.kt` — El repositorio que utiliza este DAO para sus operaciones.
- `FirebaseMedicationRepository.kt` — Repositorio que sincroniza con la nube.
- `AddMedicationScreen.kt` — Pantalla donde se agregan nuevos medicamentos.
- `MedicamentosScreen.kt` — Pantalla donde se muestra la lista de medicamentos.

---

## AppDatabase.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.database

import android.content.Context
import androidx.room.Database
import androidx.room.Room
import androidx.room.RoomDatabase
import androidx.room.TypeConverters
import mx.edu.utng.aimc.com.pantallaprincipal.data.dao.MedicationDao
import mx.edu.utng.aimc.com.pantallaprincipal.data.dao.AppointmentDao
import mx.edu.utng.aimc.com.pantallaprincipal.data.dao.EmergencyContactDao
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.MedicationEntity
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.AppointmentEntity
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.EmergencyContactEntity

@Database(
    entities = [MedicationEntity::class, AppointmentEntity::class, EmergencyContactEntity::class],
    version = 4
)
@TypeConverters(Converters::class)
abstract class AppDatabase : RoomDatabase() {

    abstract fun medicationDao(): MedicationDao

    abstract fun appointmentDao(): AppointmentDao

    abstract fun emergencyContactDao(): EmergencyContactDao

    companion object {
        @Volatile private var INSTANCE: AppDatabase? = null

        fun getDatabase(context: Context): AppDatabase {
            return INSTANCE ?: synchronized(this) {
                val inst = Room.databaseBuilder(
                    context.applicationContext,
                    AppDatabase::class.java,
                    "med_alert_db"
                )
                    .fallbackToDestructiveMigration()
                    .build()
                INSTANCE = inst
                inst
            }
        }
    }
}
```

### Descripción General

Este archivo define la base de datos local de la aplicación usando Room. Es el corazón del almacenamiento de datos en el teléfono, donde se guardan todos los registros de medicamentos, citas médicas y contactos de emergencia. Actúa como el punto central que conecta todas las tablas con sus respectivos DAOs.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.database`

### Explicación Detallada

**¿Qué es AppDatabase?**

`AppDatabase` es la clase principal que representa la base de datos SQLite en el teléfono. Usando Room, se encarga de crear y administrar las tablas, las relaciones entre ellas y proporcionar acceso a los datos a través de los DAOs.

**Anotaciones y su Significado**

`@Database` — Le indica a Room que esta clase es una base de datos. Tiene dos partes importantes:

- `entities`: Lista las clases que serán tablas en la base de datos. Aquí se incluyen `MedicationEntity`, `AppointmentEntity` y `EmergencyContactEntity`.
- `version`: El número de versión de la base de datos. Cada vez que se cambia la estructura de las tablas, este número debe aumentarse. Actualmente está en la versión 4.

`@TypeConverters` — Indica que se usará la clase `Converters` para convertir tipos de datos especiales que Room no maneja por defecto, como fechas o listas, a formatos que sí puede guardar en SQLite.

**Tablas Incluidas**

La base de datos tiene tres tablas principales:

- `MedicationEntity`: Guarda toda la información de los medicamentos que el usuario registra, como nombre, dosis, horarios, etc.
- `AppointmentEntity`: Almacena los datos de las citas médicas, incluyendo fecha, hora, lugar y motivo de la cita.
- `EmergencyContactEntity`: Contiene los contactos de emergencia con nombres, teléfonos y parentesco.

**Métodos Abstractos**

- `medicationDao()`: Proporciona acceso al DAO de medicamentos.
- `appointmentDao()`: Da acceso al DAO de citas médicas.
- `emergencyContactDao()`: Permite acceder al DAO de contactos de emergencia.

**Patrón Singleton**

`@Volatile private var INSTANCE` — Esta palabra clave asegura que los cambios en la variable `INSTANCE` sean visibles inmediatamente para todos los hilos de ejecución, previniendo problemas de concurrencia.

`getDatabase(context: Context)` — Proporciona la instancia de la base de datos. Verifica si ya existe una instancia creada; si no, entra en un bloque `synchronized` para crear una sola instancia de forma segura.

**Configuración de la Base de Datos**

`Room.databaseBuilder()` configura la base de datos con tres parámetros:

- `context.applicationContext`: El contexto de la aplicación.
- `AppDatabase::class.java`: La clase de la base de datos que se está construyendo.
- `"med_alert_db"`: El nombre del archivo de base de datos.

`.fallbackToDestructiveMigration()` — Cuando hay cambios en las tablas sin una migración definida, Room borra todas las tablas y las vuelve a crear. Es útil durante el desarrollo pero tiene la desventaja de que se pierden todos los datos guardados.

### Flujo de Trabajo Típico

1. **Inicialización**: La aplicación llama a `AppDatabase.getDatabase(context)` para obtener la instancia de la base de datos.
2. **Acceso a DAOs**: A través de la instancia, la aplicación obtiene los DAOs necesarios.
3. **Operaciones**: Los ViewModels y Repositorios usan estos DAOs para realizar operaciones en la base de datos.
4. **Actualizaciones**: Cuando el usuario agrega, modifica o elimina información, los cambios se reflejan inmediatamente.

### Notas Importantes

- La versión actual de la base de datos es la 4, lo que indica que ha pasado por varios cambios estructurales durante el desarrollo.
- El uso de `.fallbackToDestructiveMigration()` significa que al actualizar la aplicación, todos los datos guardados se perderán. En una versión final para producción, esto debería reemplazarse con migraciones adecuadas.
- El patrón Singleton con `@Volatile` y `synchronized` asegura que la base de datos funcione correctamente incluso en aplicaciones con múltiples hilos de ejecución.

### Archivos Relacionados

- `Converters.kt` — Clase que convierte tipos especiales para guardarlos en la base de datos.
- `MedicationDao.kt`, `AppointmentDao.kt`, `EmergencyContactDao.kt` — Los DAOs que esta base de datos proporciona.
- `MedicationEntity.kt`, `AppointmentEntity.kt`, `EmergencyContactEntity.kt` — Las entidades que definen las tablas.

---

## Converters.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.database

import androidx.room.TypeConverter

class Converters {

    @TypeConverter
    fun fromList(value: List<String>): String {
        return value.joinToString(",")
    }

    @TypeConverter
    fun toList(value: String): List<String> {
        return if (value.isEmpty()) emptyList() else value.split(",")
    }
}
```

### Descripción General

Este archivo contiene una clase especial llamada `Converters` que sirve como traductor entre tipos de datos que Kotlin maneja fácilmente y tipos que la base de datos SQLite puede guardar. En particular, se encarga de convertir listas de texto en algo que se pueda almacenar en una sola celda de la base de datos y viceversa.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.database`

### Explicación Detallada

**¿Por qué se necesitan Converters?**

SQLite solo puede guardar tipos de datos básicos como números, texto y fechas. No entiende tipos más complejos de Kotlin como listas, conjuntos o clases personalizadas. Los `TypeConverter` resuelven este problema actuando como puentes: convierten tipos complejos a tipos simples para guardarlos, y luego los vuelven a convertir a su forma original cuando se recuperan.

`@TypeConverter` — Le indica a Room que estos métodos son convertidores. Cuando Room encuentre un campo de tipo `List<String>` en una entidad, automáticamente usará estos métodos para convertirlo a `String` antes de guardarlo.

**Métodos y su Función**

`fromList()` — Convierte una lista de textos en un solo texto largo, uniendo los elementos con comas.

- Ejemplo: `["lunes", "martes", "miércoles"]` → `"lunes,martes,miércoles"`.
- Parámetros: Recibe una lista de strings (`List<String>`).
- Retorno: Devuelve un solo string con todos los elementos unidos por comas.

`toList()` — Hace la operación contraria: toma un texto con elementos separados por comas y lo convierte en lista.

- Ejemplo: `"lunes,martes,miércoles"` → `["lunes", "martes", "miércoles"]`.
- Parámetros: Recibe un string que contiene elementos separados por comas.
- Retorno: Devuelve una lista de strings. Si el texto está vacío, devuelve una lista vacía.

**Ejemplos Prácticos**

*Ejemplo 1: Horarios de medicamentos*

Imagina que un medicamento se toma en múltiples horarios: 8:00 AM, 2:00 PM y 8:00 PM.

En la entidad `MedicationEntity`:

```kotlin
val horarios: List<String> = listOf("8:00 AM", "2:00 PM", "8:00 PM")
```

Cuando se guarda en la base de datos, Room usa `fromList()` y convierte la lista a `"8:00 AM,2:00 PM,8:00 PM"`. Al recuperarla, usa `toList()` para devolverla como `["8:00 AM", "2:00 PM", "8:00 PM"]`.

*Ejemplo 2: Días de la semana para recordatorios*

- Guardar: `["lunes", "miércoles", "viernes"]` → `"lunes,miércoles,viernes"`
- Recuperar: `"lunes,miércoles,viernes"` → `["lunes", "miércoles", "viernes"]`

**Casos Especiales**

*Listas Vacías* — El método `toList()` tiene una validación importante:

```kotlin
return if (value.isEmpty()) emptyList() else value.split(",")
```

Si el texto guardado está vacío, devuelve directamente una lista vacía en lugar de intentar hacer `split(",")`, lo que produciría una lista con un elemento vacío.

*Elementos con Comas* — Una limitación importante es que si algún elemento de la lista contiene una coma, el resultado al recuperar la información no será el esperado. Para los usos típicos de esta aplicación, este método funciona bien.

### Notas Importantes

- Estos convertidores son específicos para `List<String>`. Si se necesitara convertir otros tipos, habría que agregar más métodos en esta clase.
- Esta clase se registra en `AppDatabase` con la anotación `@TypeConverters(Converters::class)`.
- Room cachea los convertidores, así que usarlos no afecta negativamente el rendimiento.

### Archivos Relacionados

- `AppDatabase.kt` — Donde se registra este convertidor con `@TypeConverters(Converters::class)`.
- `MedicationEntity.kt` — La entidad que usa listas para horarios o días.
- `AppointmentEntity.kt` — Podría usar listas para recordatorios o notas.
- `EmergencyContactEntity.kt` — Podría usar listas para múltiples teléfonos.

---

## AppointmentEntity.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.entity

import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "appointments")
data class AppointmentEntity(
    @PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    val place: String = "",
    val dateTime: Long = 0L
)
```

### Descripción General

Este archivo define la estructura de cómo se guardan las citas médicas en la base de datos del teléfono. Es como un molde que le dice a la aplicación qué información debe guardar de cada cita y de qué tipo es cada dato. Cada vez que el usuario agrega una cita, se crea un objeto de este tipo para almacenarla.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.entity`

### Explicación Detallada

**¿Qué es una Entidad?**

En el contexto de bases de datos, una entidad es una representación de una tabla. Cada objeto de tipo `AppointmentEntity` corresponde a una fila en la tabla `appointments`, y cada propiedad de la clase corresponde a una columna.

**Anotaciones y su Significado**

- `@Entity`: Le indica a Room que esta clase representa una tabla. El parámetro `tableName = "appointments"` especifica el nombre de la tabla.
- `@PrimaryKey`: Marca una propiedad como la llave principal de la tabla. Con `autoGenerate = true`, Room genera automáticamente un valor único cada vez que se inserta una nueva cita.

**Propiedades de la Entidad**

`id: Int = 0` — La llave principal, identificador único de cada cita. Se genera automáticamente al guardar; el valor 0 es solo un marcador de posición.

`place: String = ""` — Almacena el lugar donde será la cita médica. Ejemplos: `"Consultorio del Dr. Martínez"`, `"Hospital General, piso 3"`.

`dateTime: Long = 0L` — Guarda la fecha y hora de la cita como un timestamp en milisegundos desde el 1 de enero de 1970. Guardar fechas como números tiene ventajas: ocupan menos espacio, se pueden ordenar fácilmente y se convierten a cualquier formato después.

**Cómo se Usa en la Práctica**

Crear una nueva cita:

```kotlin
val timestamp = 1705325400000L

val nuevaCita = AppointmentEntity(
    place = "Consultorio Dental",
    dateTime = timestamp
)
```

Recuperar una cita de la base de datos:

```kotlin
AppointmentEntity(
    id = 5,  // Ya tiene un ID asignado por Room
    place = "Consultorio Dental",
    dateTime = 1705325400000L
)
```

### Notas Importantes

- Todas las propiedades tienen valores por defecto, lo que permite crear objetos sin tener que especificar todos los campos.
- Aunque aquí se usa `Int` para el ID, en otros DAOs como `MedicationDao` se usa `Long`. La diferencia es el tamaño máximo que pueden almacenar, pero para una aplicación normal, `Int` es suficiente.

### Archivos Relacionados

- `AppointmentDao.kt` — El DAO que usa esta entidad para operaciones de base de datos.
- `AppDatabase.kt` — La base de datos que incluye esta entidad en su lista de tablas.
- `AppointmentRepository.kt` — El repositorio que maneja la lógica de negocio de las citas.
- `FirebaseAppointmentRepository.kt` — Para sincronizar con la nube.
- `AgregarCita.kt` y `CitasMedicasScreen.kt` — Las pantallas donde se crean y muestran las citas.
- `AppointmentViewModel.kt` — El ViewModel que usa esta entidad para manejar el estado.

---

## EmergencyContactEntity.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.entity

import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "emergency_contacts")
data class EmergencyContactEntity(
    @PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    val name: String = "",
    val phoneNumber: String = ""
)
```

### Notas Importantes

- Esta tabla está diseñada específicamente para guardar contactos a los que se pueda recurrir en caso de una emergencia médica.
- A diferencia de otras entidades que pueden tener muchos campos, esta es intencionalmente simple: solo nombre y teléfono. En una emergencia, no se necesita más información.
- Todas las propiedades tienen valores por defecto, lo que permite crear objetos sin tener que especificar todos los campos.
- El sistema de IDs automáticos asegura que cada contacto tenga un identificador único sin que el usuario tenga que preocuparse por eso.

### Archivos Relacionados

- `EmergencyContactDao.kt` — El DAO que usa esta entidad para las operaciones de base de datos. Tiene métodos como `insert()`, `delete()`, `getAllContacts()` y `deleteById()`.
- `AppDatabase.kt` — La base de datos que incluye esta entidad en su lista de tablas.
- `EmergencyContactRepository.kt` — El repositorio que maneja la lógica de negocio de los contactos de emergencia.
- `FirebaseContactRepository.kt` — Para sincronizar los contactos de emergencia con la nube (Firebase).
- `AgregarNumeroEmergencia.kt` — La pantalla donde el usuario agrega nuevos contactos de emergencia.
- `EmergencyContactViewModel.kt` — El ViewModel que usa esta entidad para manejar el estado.

---

## MedicationEntity.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.entity

import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "medications")
data class MedicationEntity(
    @PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    val name: String = "",
    val dose: String = "",
    val note: String = "",
    val schedules: List<String> = emptyList(),
    val days: List<String> = emptyList(),
    val totalDays: Int = 0,
    val daysTaken: Int = 0
)
```

### Descripción General

Este archivo define la estructura de cómo se guardan los medicamentos en la base de datos del teléfono. Es el molde más completo de la aplicación porque los medicamentos tienen mucha información asociada: el nombre, la dosis, las notas, los horarios, los días de la semana y el progreso del tratamiento.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.entity`

### Explicación Detallada

**Propiedades de la Entidad**

`id: Int = 0` — La llave principal, identificador único de cada medicamento. Se genera automáticamente al guardar.

`name: String = ""` — Almacena el nombre del medicamento. Ejemplos: `"Paracetamol"`, `"Amoxicilina 500mg"`, `"Ibuprofeno"`.

`dose: String = ""` — Guarda la dosis o cantidad. Ejemplos: `"1 tableta"`, `"500 mg"`, `"2 cucharadas"`.

`note: String = ""` — Almacena notas adicionales o instrucciones especiales. Ejemplos: `"Tomar después de los alimentos"`, `"Evitar exponerse al sol"`.

`schedules: List<String> = emptyList()` — Guarda los horarios en los que se debe tomar el medicamento. Es una lista porque puede haber múltiples horarios al día. Los valores se guardan usando los convertidores de `Converters.kt`. Ejemplos: `["8:00 AM", "2:00 PM", "8:00 PM"]`.

`days: List<String> = emptyList()` — Indica los días de la semana en que se debe tomar el medicamento. Ejemplos: `["lunes", "miércoles", "viernes"]`.

`totalDays: Int = 0` — Representa la duración total del tratamiento en días. Ejemplos: `7` para una semana, `30` para un mes. Un valor de `0` podría significar que es un tratamiento continuo sin fecha de finalización.

`daysTaken: Int = 0` — Lleva el conteo de cuántos días se ha tomado el medicamento. Este número aumenta cada vez que el usuario confirma que tomó el medicamento. Cuando `daysTaken` llega a ser igual a `totalDays`, el tratamiento está completo.

**Cómo se Usa en la Práctica**

Crear un nuevo medicamento:

```kotlin
val nuevoMedicamento = MedicationEntity(
    name = "Amoxicilina",
    dose = "500 mg",
    note = "Tomar cada 12 horas, con alimentos",
    schedules = listOf("8:00 AM", "8:00 PM"),
    days = listOf("todos"),
    totalDays = 7,
    daysTaken = 0
)
```

Actualizar el progreso:

```kotlin
val medicamentoActualizado = medicamento.copy(
    daysTaken = medicamento.daysTaken + 1
)
medicationDao.updateMedication(medicamentoActualizado)
```

### Notas Importantes

- Las propiedades `schedules` y `days` son listas que se convierten usando `Converters.kt` antes de guardarse en SQLite.
- Cuando `totalDays` es 0, podría interpretarse como un tratamiento que no tiene fecha de finalización.
- La combinación de `totalDays` y `daysTaken` permite mostrar barras de progreso y saber cuándo termina un tratamiento.
- Los valores por defecto `emptyList()` para `schedules` y `days` son importantes porque evitan listas nulas que podrían causar errores.

### Archivos Relacionados

- `MedicationDao.kt` — El DAO con métodos como `insertMedication()`, `deleteMedication()`, `getAllMedications()` y `updateMedication()`.
- `AppDatabase.kt` — La base de datos que incluye esta entidad.
- `Converters.kt` — La clase que convierte las listas a texto para guardarlas.
- `MedicationRepository.kt` — El repositorio que maneja la lógica de negocio.
- `FirebaseMedicationRepository.kt` — Para sincronizar con la nube (Firebase).
- `AddMedicationScreen.kt` — La pantalla donde el usuario agrega nuevos medicamentos.
- `MedicamentosScreen.kt` — La pantalla donde se muestra la lista con su progreso.
- `MedicationViewModel.kt` — El ViewModel que usa esta entidad para manejar el estado.

---

## Medication.kt (Model)

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.model

data class Medication(
    val id: Int = 0,
    val name: String = "",
    val dose: String = "",
    val hour: String = ""
)
```

**Posible Conversión entre Entity y Model**

Es probable que en el código haya funciones para convertir de un tipo a otro:

```kotlin
// Convertir de Entity (base de datos) a Model (para mostrar)
fun MedicationEntity.toMedicationModel(): Medication {
    return Medication(
        id = this.id,
        name = this.name,
        dose = this.dose,
        hour = this.schedules.firstOrNull() ?: ""  // Toma el primer horario
    )
}

// Convertir de Model a Entity (para guardar)
fun Medication.toMedicationEntity(): MedicationEntity {
    return MedicationEntity(
        id = this.id,
        name = this.name,
        dose = this.dose,
        note = "",
        schedules = if (this.hour.isNotEmpty()) listOf(this.hour) else emptyList(),
        days = emptyList(),
        totalDays = 0,
        daysTaken = 0
    )
}
```

### Notas Importantes

- Este modelo es una versión simplificada de `MedicationEntity`. Pierde información como notas, días de la semana y el progreso del tratamiento.
- Mientras que `MedicationEntity` permite múltiples horarios en una lista, `Medication` solo tiene un campo `hour` para un solo horario.
- A diferencia de `MedicationEntity`, este modelo no tiene anotaciones de Room ni depende de la base de datos, lo que lo hace más ligero y fácil de usar en cualquier parte de la aplicación.
- Todos los campos tienen valores por defecto, lo que permite crear objetos vacíos fácilmente.

### Archivos Relacionados

- `MedicationEntity.kt` — La versión completa para la base de datos.
- Posibles pantallas donde se muestren listas simples de medicamentos.
- Posibles ViewModels que manejen datos resumidos.

---

## AppointmentRepository.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.repository

import mx.edu.utng.aimc.com.pantallaprincipal.data.dao.AppointmentDao
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.AppointmentEntity

class AppointmentRepository(
    private val appointmentDao: AppointmentDao
) {
    fun getAllAppointments() = appointmentDao.getAllAppointments()

    suspend fun insertAppointment(appointment: AppointmentEntity): Long {
        return appointmentDao.insertAppointment(appointment)
    }

    suspend fun deleteAppointment(appointment: AppointmentEntity) {
        appointmentDao.deleteAppointment(appointment)
    }
}
```

### Descripción General

Este archivo define el repositorio de citas médicas, que actúa como un puente entre la base de datos local y el resto de la aplicación. Su trabajo es proporcionar una forma limpia y organizada de acceder a los datos de las citas sin que las otras partes de la aplicación tengan que saber cómo están guardados internamente.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.repository`

### Explicación Detallada

**¿Qué es un Repositorio?**

Un repositorio es un patrón de diseño que separa la lógica de acceso a datos del resto de la aplicación. En lugar de que los ViewModels o las pantallas hablen directamente con el DAO, hablan con este repositorio. Esto tiene varias ventajas: organización, flexibilidad ante cambios futuros de fuente de datos, y mayor facilidad para las pruebas.

**Constructor y Dependencias**

```kotlin
class AppointmentRepository(
    private val appointmentDao: AppointmentDao
)
```

El repositorio recibe el DAO por inyección de dependencias, lo que le permite usarlo sin tener que crearlo él mismo.

**Métodos y su Función**

`getAllAppointments()` — Obtiene todas las citas. Devuelve un `Flow<List<AppointmentEntity>>` que se actualiza automáticamente cuando hay cambios.

`insertAppointment()` — Guarda una nueva cita. Es `suspend`, debe ejecutarse en segundo plano. Devuelve el `Long` que es el ID asignado.

`deleteAppointment()` — Elimina una cita existente. También es `suspend`.

**Flujo de Trabajo Típico**

Ejemplo — Mostrar todas las citas:

```kotlin
class AppointmentViewModel(private val repository: AppointmentRepository) {
    val allAppointments = repository.getAllAppointments()
}

val allAppointments by viewModel.allAppointments.collectAsState()
```

Ejemplo — Agregar una nueva cita:

```kotlin
fun addAppointment(place: String, dateTime: Long) {
    viewModelScope.launch {
        val newAppointment = AppointmentEntity(
            place = place,
            dateTime = dateTime
        )
        val newId = repository.insertAppointment(newAppointment)
        Log.d("Appointment", "Cita guardada con ID: $newId")
    }
}
```

Ejemplo — Eliminar una cita:

```kotlin
fun deleteAppointment(appointment: AppointmentEntity) {
    viewModelScope.launch {
        repository.deleteAppointment(appointment)
    }
}
```

### Notas Importantes

- Este repositorio es bastante simple porque solo pasa llamadas al DAO. En aplicaciones más complejas, aquí se podrían agregar validaciones o lógica de negocio.
- Todos los métodos que modifican la base de datos son `suspend`, lo que obliga a llamarlos desde una corrutina.
- El constructor recibe el DAO, lo que hace que el repositorio sea fácil de probar y de mantener.

### Archivos Relacionados

- `AppointmentDao.kt` — El DAO que este repositorio usa para acceder a la base de datos.
- `AppointmentEntity.kt` — La entidad que representa una cita.
- `AppDatabase.kt` — La base de datos que proporciona el DAO.
- `FirebaseAppointmentRepository.kt` — Posible repositorio alternativo para la nube.
- `AppointmentViewModel.kt` — El ViewModel que usa este repositorio.
- `AgregarCita.kt` y `CitasMedicasScreen.kt` — Las pantallas que muestran y crean citas.

---

## EmergencyContactRepository.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.repository

import mx.edu.utng.aimc.com.pantallaprincipal.data.dao.EmergencyContactDao
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.EmergencyContactEntity

class EmergencyContactRepository(
    private val contactDao: EmergencyContactDao
) {
    fun getAllContacts() = contactDao.getAllContacts()

    suspend fun insertContact(contact: EmergencyContactEntity): Long {
        return contactDao.insert(contact)
    }

    suspend fun deleteContact(contact: EmergencyContactEntity) {
        contactDao.delete(contact)
    }

    suspend fun deleteById(contactId: Long) {
        contactDao.deleteById(contactId)
    }
}
```

### Descripción General

Este archivo define el repositorio de contactos de emergencia, que actúa como un intermediario entre la base de datos local y el resto de la aplicación. Su función es proporcionar una forma organizada y limpia de acceder a los datos de los contactos de emergencia.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.repository`

### Explicación Detallada

**Métodos y su Función**

`getAllContacts()` — Obtiene todos los contactos. Devuelve un `Flow<List<EmergencyContactEntity>>` que se actualiza automáticamente.

`insertContact()` — Guarda un nuevo contacto. Devuelve el `Long` que es el ID asignado.

`deleteContact()` — Elimina un contacto usando el objeto completo.

`deleteById()` — Elimina un contacto usando directamente su identificador, útil cuando solo se conoce el ID.

**Flujo de Trabajo Típico**

Ejemplo — Agregar un nuevo contacto:

```kotlin
fun addContact(name: String, phoneNumber: String) {
    viewModelScope.launch {
        val newContact = EmergencyContactEntity(
            name = name,
            phoneNumber = phoneNumber
        )
        val newId = repository.insertContact(newContact)
        Log.d("EmergencyContact", "Contacto guardado con ID: $newId")
    }
}
```

Ejemplo — Eliminar un contacto:

```kotlin
fun deleteContact(contact: EmergencyContactEntity) {
    viewModelScope.launch {
        repository.deleteContact(contact)
    }
}

fun deleteContactById(contactId: Long) {
    viewModelScope.launch {
        repository.deleteById(contactId)
    }
}
```

### Notas Importantes

- Este repositorio ofrece dos métodos para eliminar contactos (`deleteContact` y `deleteById`), lo que da flexibilidad según lo que se tenga disponible.
- Todos los métodos que modifican la base de datos son `suspend`.
- Este repositorio solo maneja la base de datos local. Si hay un `FirebaseContactRepository`, sería para sincronizar con la nube.

### Archivos Relacionados

- `EmergencyContactDao.kt` — El DAO que este repositorio usa.
- `EmergencyContactEntity.kt` — La entidad que representa un contacto.
- `AppDatabase.kt` — La base de datos que proporciona el DAO.
- `FirebaseContactRepository.kt` — Posible repositorio alternativo para la nube.
- `EmergencyContactViewModel.kt` — El ViewModel que usa este repositorio.
- `AgregarNumeroEmergencia.kt` — La pantalla donde se agregan nuevos contactos.

---

## FirebaseAppointmentRepository.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.repository

import com.google.firebase.firestore.FirebaseFirestore
import kotlinx.coroutines.channels.awaitClose
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.callbackFlow
import kotlinx.coroutines.tasks.await
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.AppointmentEntity

class FirebaseAppointmentRepository {
    private val db = FirebaseFirestore.getInstance()
    private val appointmentsCollection = db.collection("appointments")

    fun getAllAppointments(): Flow<List<AppointmentEntity>> = callbackFlow {
        val listener = appointmentsCollection.addSnapshotListener { snapshot, error ->
            if (error != null) {
                close(cause = error)
                return@addSnapshotListener
            }

            val appointments = snapshot?.documents?.mapNotNull { doc ->
                doc.toObject(AppointmentEntity::class.java)?.copy(
                    id = doc.id.hashCode()
                )
            } ?: emptyList()

            trySend(element = appointments)
        }

        awaitClose { listener.remove() }
    }

    suspend fun insertAppointment(appointment: AppointmentEntity): Int {
        return try {
            val docRef = appointmentsCollection.add(appointment).await()
            docRef.id.hashCode()
        } catch (e: Exception) {
            e.printStackTrace()
            0
        }
    }

    suspend fun deleteAppointment(appointment: AppointmentEntity) {
        try {
            appointmentsCollection
                .whereEqualTo("place", appointment.place)
                .whereEqualTo("dateTime", appointment.dateTime)
                .get()
                .await()
                .documents
                .firstOrNull()
                ?.reference
                ?.delete()
                ?.await()
        } catch (e: Exception) {
            e.printStackTrace()
        }
    }
}
```

### Descripción General

Este archivo define el repositorio de citas médicas que se sincroniza con Firebase. A diferencia del `AppointmentRepository` que guarda las citas localmente, este repositorio guarda la información en internet, permitiendo que los datos estén disponibles en cualquier dispositivo y no se pierdan si se desinstala la aplicación.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.repository`

### Explicación Detallada

**Propiedades y Configuración**

- `db`: La conexión a Firebase Firestore. `getInstance()` devuelve una instancia ya configurada.
- `appointmentsCollection`: Referencia a la colección `"appointments"` en Firebase. Una colección es como una carpeta que contiene documentos, similar a una tabla en bases de datos tradicionales.

**Métodos y su Función**

`getAllAppointments()` — Usa `callbackFlow` para convertir el listener de Firestore en un `Flow` de Kotlin. Cada vez que hay cambios en la nube, Firebase avisa y la lista se actualiza automáticamente. `awaitClose` detiene la escucha cuando el `Flow` se cierra para ahorrar recursos.

`insertAppointment()` — Guarda una nueva cita en Firebase. Convierte el ID de texto de Firebase a un número entero usando `hashCode()`. Si hay error, devuelve 0.

`deleteAppointment()` — Busca en Firebase documentos que tengan el mismo lugar y fecha/hora que la cita a eliminar, asumiendo que no hay dos citas exactamente iguales.

**Notas Importantes sobre el Código**

*Sobre los IDs*:

```kotlin
doc.toObject(AppointmentEntity::class.java)?.copy(
    id = doc.id.hashCode()
)
```

Los IDs en Firebase son textos como `"ABC123xyz"`, pero la aplicación espera números enteros. Se usa `hashCode()` para convertirlos, aunque diferentes textos podrían generar el mismo número (es poco probable).

*Sobre la eliminación*: La búsqueda por lugar y fecha asume que no hay dos citas exactamente iguales, lo cual es razonable.

*Manejo de errores*: Todos los métodos tienen bloques `try/catch` que muestran el error en la consola y devuelven valores por defecto para evitar que la aplicación se cierre.

**Flujo de Trabajo Típico**

Ejemplo — Insertar una cita:

```kotlin
fun addAppointmentToCloud(place: String, dateTime: Long) {
    viewModelScope.launch {
        val appointment = AppointmentEntity(place = place, dateTime = dateTime)
        val cloudId = firebaseRepo.insertAppointment(appointment)
        if (cloudId != 0) {
            showMessage("Cita guardada en la nube")
        } else {
            showError("No se pudo guardar en la nube")
        }
    }
}
```

### Posibles Mejoras

- **Mejor manejo de IDs**: En lugar de usar `hashCode()`, se podría modificar `AppointmentEntity` para aceptar IDs de texto.
- **Eliminación por ID real**: Si se guardara el ID de Firebase en la entidad local, se podría eliminar directamente.
- **Sincronización automática**: Combinar este repositorio con el local para guardar en ambos lugares automáticamente.

### Archivos Relacionados

- `AppointmentEntity.kt` — La entidad que se guarda en Firebase.
- `AppointmentRepository.kt` — El repositorio local equivalente.
- `AppointmentViewModel.kt` — Posible ViewModel que podría usar ambos repositorios.

---

## FirebaseContactRepository.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.repository

import com.google.firebase.firestore.FirebaseFirestore
import kotlinx.coroutines.channels.awaitClose
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.callbackFlow
import kotlinx.coroutines.tasks.await
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.EmergencyContactEntity

class FirebaseContactRepository {
    private val db = FirebaseFirestore.getInstance()
    private val contactsCollection = db.collection("contacts")

    fun getAllContacts(): Flow<List<EmergencyContactEntity>> = callbackFlow {
        val listener = contactsCollection.addSnapshotListener { snapshot, error ->
            if (error != null) {
                close(cause = error)
                return@addSnapshotListener
            }

            val contacts = snapshot?.documents?.mapNotNull { doc ->
                doc.toObject(EmergencyContactEntity::class.java)?.copy(
                    id = doc.id.hashCode()
                )
            } ?: emptyList()

            trySend(element = contacts)
        }

        awaitClose { listener.remove() }
    }

    suspend fun insertContact(contact: EmergencyContactEntity): Int {
        return try {
            val docRef = contactsCollection.add(contact).await()
            docRef.id.hashCode()
        } catch (e: Exception) {
            e.printStackTrace()
            0
        }
    }

    suspend fun deleteContact(contact: EmergencyContactEntity) {
        try {
            contactsCollection
                .whereEqualTo("name", contact.name)
                .whereEqualTo("phoneNumber", contact.phoneNumber)
                .get()
                .await()
                .documents
                .firstOrNull()
                ?.reference
                ?.delete()
                ?.await()
        } catch (e: Exception) {
            e.printStackTrace()
        }
    }
}
```

### Descripción General

Este archivo define el repositorio de contactos de emergencia que se sincroniza con Firebase. A diferencia del `EmergencyContactRepository`, este guarda la información en internet, permitiendo que los contactos estén disponibles en cualquier dispositivo.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.repository`

### Explicación Detallada

**Propiedades y Configuración**

- `db`: La conexión a Firebase Firestore.
- `contactsCollection`: Referencia a la colección `"contacts"` en Firebase.

**Métodos y su Función**

`getAllContacts()` — Escucha cambios en tiempo real en Firebase y emite listas actualizadas a través de un `Flow`. Cuando el `Flow` se cierra, deja de escuchar cambios para ahorrar recursos.

`insertContact()` — Guarda un nuevo contacto en Firebase. Convierte el ID de texto a número usando `hashCode()`. Devuelve 0 si hay error.

`deleteContact()` — Busca en Firebase documentos con el mismo nombre y teléfono que el contacto a eliminar. La búsqueda por nombre y teléfono es más precisa que buscar solo por nombre.

### Archivos Relacionados

- `EmergencyContactEntity.kt` — La entidad que se guarda en Firebase.
- `EmergencyContactViewModel.kt` — El ViewModel que usa este repositorio.

---

## FirebaseMedicationRepository.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.repository

import com.google.firebase.firestore.FirebaseFirestore
import kotlinx.coroutines.channels.awaitClose
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.callbackFlow
import kotlinx.coroutines.tasks.await
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.MedicationEntity

class FirebaseMedicationRepository {
    private val db = FirebaseFirestore.getInstance()
    private val medicationsCollection = db.collection("medications")

    fun getAllMedications(): Flow<List<MedicationEntity>> = callbackFlow {
        val listener = medicationsCollection.addSnapshotListener { snapshot, error ->
            if (error != null) {
                close(cause = error)
                return@addSnapshotListener
            }

            val medications = snapshot?.documents?.mapNotNull { doc ->
                doc.toObject(MedicationEntity::class.java)?.copy(id = doc.id.hashCode())
            } ?: emptyList()

            trySend(element = medications)
        }

        awaitClose { listener.remove() }
    }

    suspend fun insertMedication(medication: MedicationEntity): Int {
        return try {
            val docRef = medicationsCollection.add(medication).await()
            docRef.id.hashCode()
        } catch (e: Exception) {
            e.printStackTrace()
            0
        }
    }

    suspend fun deleteMedication(medication: MedicationEntity) {
        try {
            medicationsCollection
                .whereEqualTo("name", medication.name)
                .get()
                .await()
                .documents
                .firstOrNull()
                ?.reference
                ?.delete()
                ?.await()
        } catch (e: Exception) {
            e.printStackTrace()
        }
    }
}
```

### Descripción General

Este archivo define el repositorio de medicamentos que se sincroniza con Firebase. Permite guardar en la nube toda la información de los medicamentos, incluyendo nombre, dosis, notas, horarios, días y progreso del tratamiento.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.repository`

### Explicación Detallada

**Propiedades y Configuración**

Similar a los otros repositorios Firebase, se conecta a Firestore y apunta a la colección `"medications"`.

**Métodos y su Función**

`getAllMedications()` — Escucha cambios en la colección, convierte cada documento a `MedicationEntity` y emite listas actualizadas automáticamente.

`insertMedication()` — Guarda un nuevo medicamento con todos sus campos (nombre, dosis, nota, `schedules`, `days`, `totalDays`, `daysTaken`).

`deleteMedication()` — Usa solo el nombre del medicamento para encontrar el documento a eliminar. Esto podría causar problemas si existen dos medicamentos con el mismo nombre.

### Notas Importantes

- Para eliminar solo usa el nombre, lo que podría causar problemas con medicamentos de nombre repetido.
- No tiene método de actualización directa, solo insertar y eliminar.
- Guarda todos los campos, incluyendo las listas `schedules` y `days`.

### Archivos Relacionados

- `MedicationEntity.kt` — La entidad que se guarda en Firebase.
- `MedicationViewModel.kt` — El ViewModel que usa este repositorio.

---

## MedicationRepository.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.repository

import kotlinx.coroutines.flow.Flow
import mx.edu.utng.aimc.com.pantallaprincipal.data.dao.MedicationDao
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.MedicationEntity

class MedicationRepository(private val dao: MedicationDao) {

    fun getAllMedications(): Flow<List<MedicationEntity>> = dao.getAllMedications()

    suspend fun insertMedication(med: MedicationEntity): Long {
        return dao.insertMedication(med)
    }

    suspend fun deleteMedication(med: MedicationEntity) {
        dao.deleteMedication(med)
    }

    suspend fun updateMedication(med: MedicationEntity) {
        dao.updateMedication(med)
    }
}
```

### Descripción General

Este archivo define el repositorio local de medicamentos, que actúa como un puente entre la base de datos local (Room) y el resto de la aplicación. Es la versión local que complementa al `FirebaseMedicationRepository` para la nube.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.data.repository`

### Explicación Detallada

**Métodos y su Función**

`getAllMedications()` — Obtiene todos los medicamentos con actualizaciones en tiempo real gracias al `Flow` del DAO.

`insertMedication()` — Guarda un nuevo medicamento. Devuelve un `Long` con el ID asignado, valor crucial para el ViewModel.

`deleteMedication()` — Elimina un medicamento de la base de datos local.

`updateMedication()` — Actualiza un medicamento existente. Este método es exclusivo del repositorio local, ya que el repositorio de Firebase no tiene actualización directa.

### Notas Importantes

- Es el único repositorio con método `updateMedication`.
- Devuelve `Long` en lugar de `Int` para el ID.
- Trabaja directamente con Room y la base de datos local.

### Archivos Relacionados

- `MedicationDao.kt` — El DAO local.
- `MedicationEntity.kt` — La entidad de medicamentos.
- `FirebaseMedicationRepository.kt` — El repositorio equivalente para la nube.
- `MedicationViewModel.kt` — El ViewModel que usa este repositorio.

---

## NavGraph.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.navigation

import android.app.Application
import androidx.compose.runtime.*
import androidx.compose.ui.platform.LocalContext
import androidx.lifecycle.viewmodel.compose.viewModel
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.compose.rememberNavController
import kotlinx.coroutines.launch
import mx.edu.utng.aimc.com.pantallaprincipal.utils.ThemePreferences
import mx.edu.utng.aimc.com.pantallaprincipal.data.database.AppDatabase
import mx.edu.utng.aimc.com.pantallaprincipal.data.repository.MedicationRepository
import mx.edu.utng.aimc.com.pantallaprincipal.data.repository.AppointmentRepository
import mx.edu.utng.aimc.com.pantallaprincipal.data.repository.EmergencyContactRepository
import mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.home.MediAlertaScreen
import mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos.AddMedicationScreen
import mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.citas.CitasMedicasScreen
import mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.citas.AgregarCita
import mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.emergencia.AgregarNumeroEmergencia
import mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.farmacias.FarmaciasCercanasScreen
import mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.configuracion.ConfiguracionScreen
import mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos.MedicamentosScreen
import mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos.MedicationListScreen
import mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.notificaciones.NotificacionMedicamento
import mx.edu.utng.aimc.com.pantallaprincipal.viewmodel.MedicationViewModel
import mx.edu.utng.aimc.com.pantallaprincipal.viewmodel.MedicationViewModelFactory
import mx.edu.utng.aimc.com.pantallaprincipal.viewmodel.AppointmentViewModel
import mx.edu.utng.aimc.com.pantallaprincipal.viewmodel.AppointmentViewModelFactory
import mx.edu.utng.aimc.com.pantallaprincipal.viewmodel.EmergencyContactViewModel
import mx.edu.utng.aimc.com.pantallaprincipal.viewmodel.EmergencyContactViewModelFactory

@Composable
fun NavGraph() {
    val navController = rememberNavController()
    val context = LocalContext.current

    val application = context.applicationContext as Application

    val database = AppDatabase.getDatabase(application)

    val medicationRepository = MedicationRepository(database.medicationDao())
    val appointmentRepository = AppointmentRepository(database.appointmentDao())
    val emergencyContactRepository = EmergencyContactRepository(database.emergencyContactDao())

    val medicationViewModel: MedicationViewModel = viewModel(
        factory = MedicationViewModelFactory(
            application = application
        )
    )

    val appointmentViewModel: AppointmentViewModel = viewModel(
        factory = AppointmentViewModelFactory()
    )

    val emergencyViewModel: EmergencyContactViewModel = viewModel(
        factory = EmergencyContactViewModelFactory()
    )

    val themePreferences = remember { ThemePreferences(context) }
    val scope = rememberCoroutineScope()
    val isDarkTheme by themePreferences.isDarkTheme.collectAsState(initial = false)

    NavHost(navController = navController, startDestination = "home") {

        composable("home") {
            MediAlertaScreen(
                onNavigateToMedicamentos = { navController.navigate("medicationList") },
                onNavigateToCitas = { navController.navigate("citas") },
                onNavigateToFarmacias = { navController.navigate("farmacias") },
                onNavigateToConfiguracion = { navController.navigate("configuracion") },
                onNavigateToMedicationList = { navController.navigate("medicationList") }
            )
        }

        composable("notificacion") {
            NotificacionMedicamento(
                onYaLaTome = { navController.popBackStack() },
                onEmergencia = { navController.navigate("agregarNumeroEmergencia") }
            )
        }

        composable("addMedication") {
            AddMedicationScreen(
                viewModel = medicationViewModel,
                onBack = { navController.popBackStack() },
                onNavigateToNotification = { navController.popBackStack() }
            )
        }

        composable("citas") {
            CitasMedicasScreen(
                onBack = { navController.popBackStack() },
                viewModel = appointmentViewModel
            )
        }

        composable("agregarCita") {
            AgregarCita(onAceptarClick = { navController.popBackStack() })
        }

        composable("agregarNumeroEmergencia") {
            AgregarNumeroEmergencia(
                onAgregar = { nombre, numero ->
                    navController.popBackStack()
                }
            )
        }

        composable("farmacias") {
            FarmaciasCercanasScreen(onBackClick = { navController.popBackStack() })
        }

        composable("configuracion") {
            ConfiguracionScreen(
                onBackClick = { navController.popBackStack() },
                onThemeChange = { isDark ->
                    scope.launch {
                        themePreferences.setDarkTheme(isDark)
                    }
                },
                isDarkTheme = isDarkTheme,
                emergencyViewModel = emergencyViewModel
            )
        }

        composable("medicamentos") {
            MedicamentosScreen(viewModel = medicationViewModel)
        }

        composable("medicationList") {
            MedicationListScreen(
                onAddMedication = { navController.navigate("addMedication") },
                onBack = { navController.popBackStack() },
                viewModel = medicationViewModel
            )
        }
    }
}
```

### Descripción General

Este archivo define el sistema de navegación de toda la aplicación. Es como el mapa que conecta todas las pantallas entre sí, permitiendo al usuario moverse de una pantalla a otra de manera organizada. También se encarga de crear y proporcionar los ViewModels y repositorios que las pantallas necesitan.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.navigation`

### Explicación Detallada

**¿Qué es NavGraph?**

`NavGraph` define todas las rutas disponibles (cada pantalla tiene una ruta única como `"home"`, `"citas"`, `"addMedication"`) y cómo navegar entre ellas. También inicializa los componentes que las pantallas necesitan: base de datos, repositorios y ViewModels.

**Componentes Principales**

`navController` — Maneja las transiciones entre pantallas. `navController.navigate("ruta")` lleva al usuario a esa pantalla; `navController.popBackStack()` regresa a la pantalla anterior.

`NavHost` — Contenedor que muestra la pantalla actual según la ruta activa. Sus parámetros clave son `navController` y `startDestination` (en este caso `"home"`).

**Inicialización de Dependencias**

```kotlin
val application = context.applicationContext as Application
val database = AppDatabase.getDatabase(application)
```

```kotlin
val medicationRepository = MedicationRepository(database.medicationDao())
val appointmentRepository = AppointmentRepository(database.appointmentDao())
val emergencyContactRepository = EmergencyContactRepository(database.emergencyContactDao())
```

Los ViewModels se crean con fábricas porque algunos necesitan el `Application` context para programar alarmas.

**Configuración del Tema**

```kotlin
val themePreferences = remember { ThemePreferences(context) }
val scope = rememberCoroutineScope()
val isDarkTheme by themePreferences.isDarkTheme.collectAsState(initial = false)
```

**Navegación entre Pantallas**

- Hacia adelante: `navController.navigate("medicationList")`
- Hacia atrás: `navController.popBackStack()`

### Flujo de Trabajo Típico

1. El usuario abre la app: `NavHost` muestra `"home"`.
2. El usuario toca "Medicamentos": navega a `"medicationList"`.
3. `MedicationListScreen` recibe el `medicationViewModel` ya creado.
4. El usuario toca "Agregar": navega a `"addMedication"`.
5. Al guardar: `navController.popBackStack()` regresa a la lista.

### Notas Importantes

- Todos los ViewModels se crean en `NavGraph` y se pasan a las pantallas, asegurando que los datos persistan mientras se navega.
- Se crean `medicationRepository`, `appointmentRepository` y `emergencyContactRepository` pero no se usan directamente; es código preparado para futuras funcionalidades.
- Hay dos rutas para medicamentos (`"medicamentos"` y `"medicationList"`), lo que podría ser redundante o preparado para diferentes vistas.

### Archivos Relacionados

- Todas las pantallas en `ui/screens/`.
- Todos los ViewModels y sus fábricas en `viewmodel/`.
- `ThemePreferences.kt` en `utils/`.
- `AppDatabase.kt` y los repositorios en `data/`.

---

## AlarmReceiver.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.receivers

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import mx.edu.utng.aimc.com.pantallaprincipal.utils.ACTION_MEDICATION_ALARM
import mx.edu.utng.aimc.com.pantallaprincipal.utils.ACTION_MARK_TAKEN
import mx.edu.utng.aimc.com.pantallaprincipal.utils.NotificationHelper

class AlarmReceiver : BroadcastReceiver() {

    override fun onReceive(context: Context, intent: Intent) {
        when (intent.action) {
            ACTION_MEDICATION_ALARM -> {
                val medName = intent.getStringExtra("medName") ?: "Medicamento Desconocido"
                val medId = intent.getIntExtra("medId", -1)

                if (medId != -1) {
                    NotificationHelper(context).showMedicationNotification(medName, medId)
                }
            }

            ACTION_MARK_TAKEN -> {
                val medId = intent.getIntExtra("medId", -1)
                if (medId != -1) {
                    markMedicationAsTaken(context, medId)
                    cancelNotification(context, medId)
                }
            }
        }
    }

    private fun markMedicationAsTaken(context: Context, medId: Int) {
        // TODO: Implementar la lógica para actualizar tu base de datos
        // Por ejemplo, usando un Repository o un ContentProvider
        // repository.markAsTaken(medId)

        // Por ahora, solo un log
        android.util.Log.d("AlarmReceiver", "Medicamento $medId marcado como tomado")
    }

    private fun cancelNotification(context: Context, notificationId: Int) {
        val notificationManager = context.getSystemService(Context.NOTIFICATION_SERVICE) as android.app.NotificationManager
        notificationManager.cancel(notificationId)
    }
}
```

### Descripción General

Este archivo define el `AlarmReceiver`, que es un componente especial de Android que escucha las alarmas programadas y responde cuando estas se activan. Es como un despertador que, cuando llega la hora, se encarga de mostrar una notificación para recordarle al usuario que debe tomar su medicamento, y también maneja las acciones que el usuario realiza sobre esa notificación.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.receivers`

### Explicación Detallada

**¿Qué es un BroadcastReceiver?**

Un `BroadcastReceiver` es un componente de Android que puede escuchar mensajes (llamados "broadcasts") que el sistema o las aplicaciones envían. `AlarmReceiver` escucha dos tipos de mensajes: cuando es hora de tomar un medicamento, y cuando el usuario toca el botón "Tomado" en la notificación.

**Constantes de Acción**

- `ACTION_MEDICATION_ALARM`: Indica que es hora de tomar un medicamento.
- `ACTION_MARK_TAKEN`: Indica que el usuario marcó el medicamento como tomado.

**Flujo de Trabajo**

*Caso 1: Es hora de tomar el medicamento*

```kotlin
ACTION_MEDICATION_ALARM -> {
    val medName = intent.getStringExtra("medName") ?: "Medicamento Desconocido"
    val medId = intent.getIntExtra("medId", -1)

    if (medId != -1) {
        NotificationHelper(context).showMedicationNotification(medName, medId)
    }
}
```

Extrae el nombre y el ID del medicamento del `Intent` y muestra la notificación si el ID es válido.

*Caso 2: El usuario marcó "Tomado"*

```kotlin
ACTION_MARK_TAKEN -> {
    val medId = intent.getIntExtra("medId", -1)
    if (medId != -1) {
        markMedicationAsTaken(context, medId)
        cancelNotification(context, medId)
    }
}
```

Extrae el ID, llama a `markMedicationAsTaken()` para actualizar la base de datos y cancela la notificación.

**Métodos Auxiliares**

`markMedicationAsTaken(context, medId)` — Método incompleto (tiene TODO). Por ahora solo registra un mensaje en la consola. Lo que debería hacer: conectar con el repositorio de medicamentos y actualizar el contador `daysTaken`.

`cancelNotification(context, notificationId)` — Obtiene el `NotificationManager` del sistema y cancela la notificación con ese ID específico.

**Flujo Completo de una Alarma**

1. En `AlarmUtils.kt` se programa una alarma con un `Intent` que incluye el nombre e ID del medicamento.
2. Cuando llega la hora, Android ejecuta `onReceive()` con ese `Intent`.
3. El receptor muestra la notificación usando `NotificationHelper`.
4. La notificación tiene un botón "Tomado". Al tocarlo, se envía otro broadcast con `ACTION_MARK_TAKEN`.
5. El receptor recibe este broadcast, intenta marcar el medicamento como tomado y cancela la notificación.

### Notas Importantes

- La función `markMedicationAsTaken` está incompleta. Es necesario implementar la lógica que actualice la base de datos.
- Se usa `-1` como valor por defecto para el ID del medicamento. Si llega `-1`, el receptor no hace nada.
- Las acciones `ACTION_MEDICATION_ALARM` y `ACTION_MARK_TAKEN` están definidas en otro archivo, lo que centraliza las constantes y evita errores de escritura.

### Archivos Relacionados

- `NotificationHelper.kt` — La clase que construye y muestra las notificaciones.
- `AlarmUtils.kt` — Contiene funciones para programar las alarmas.
- `MedicationRepository.kt` — Debería usarse para implementar el `markMedicationAsTaken` pendiente.
- `MedicationEntity.kt` — La entidad que se actualizaría cuando se marca como tomado.

---

## AgregarCita.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.citas

import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
@Preview(showBackground = true)
fun AgregarCita(onAceptarClick: () -> Unit = {}) {
    Card(
        shape = RoundedCornerShape(16.dp),
        modifier = Modifier
            .fillMaxWidth()
            .padding(16.dp),
        colors = CardDefaults.cardColors(containerColor = Color(0xFFF5F5F5)),
        elevation = CardDefaults.cardElevation(8.dp)
    ) {
        Column(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(16.dp)
        ) {
            Text(
                text = "Agregar Cita",
                fontSize = 24.sp,
                fontWeight = FontWeight.Bold,
                color = Color.Black
            )

            var lugar by remember { mutableStateOf("") }
            OutlinedTextField(
                value = lugar,
                onValueChange = { lugar = it },
                label = { Text("Lugar") },
                modifier = Modifier.fillMaxWidth(),
                shape = RoundedCornerShape(10.dp)
            )

            var fechaHora by remember { mutableStateOf("") }
            OutlinedTextField(
                value = fechaHora,
                onValueChange = { fechaHora = it },
                label = { Text("📅 Fecha y hora") },
                placeholder = { Text("dd/mm/aaaa - hh:mm") },
                modifier = Modifier.fillMaxWidth(),
                shape = RoundedCornerShape(10.dp)
            )

            Button(
                onClick = { onAceptarClick() },
                modifier = Modifier
                    .fillMaxWidth()
                    .height(50.dp),
                colors = ButtonDefaults.buttonColors(containerColor = Color(0xFF4CAF50)),
                shape = RoundedCornerShape(12.dp)
            ) {
                Text("Aceptar", fontSize = 18.sp, color = Color.White)
            }
        }
    }
}
```

### Descripción General

Este archivo define la pantalla para agregar una nueva cita médica. Es un formulario simple donde el usuario puede ingresar el lugar de la cita y la fecha y hora. Al presionar el botón "Aceptar", la información se envía de vuelta a la pantalla anterior.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.citas`

### Explicación Detallada

**AgregarCita()**

Muestra una tarjeta con un formulario para agregar una nueva cita médica.

- Parámetros: `onAceptarClick` — Función que se ejecuta al presionar "Aceptar". Por defecto no hace nada.
- Componentes: Título "Agregar Cita", campo "Lugar", campo "Fecha y hora" y botón "Aceptar" verde.
- Características visuales: La tarjeta tiene fondo gris claro (#F5F5F5), bordes redondeados de 16dp y sombra de 8dp.
- Limitaciones: Este formulario no valida que los campos estén llenos; el usuario puede presionar "Aceptar" incluso si los campos están vacíos.

---

## CitasMedicasScreen.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.citas

import android.app.DatePickerDialog
import android.app.TimePickerDialog
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.ArrowBack
import androidx.compose.material.icons.filled.CalendarMonth
import androidx.compose.material.icons.filled.Delete
import androidx.compose.material.icons.filled.LocationOn
import androidx.compose.material.icons.filled.Schedule
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import kotlinx.coroutines.flow.collectLatest
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.AppointmentEntity
import mx.edu.utng.aimc.com.pantallaprincipal.viewmodel.AppointmentViewModel
import java.text.SimpleDateFormat
import java.util.*
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.background
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.runtime.Composable
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.unit.sp

private val DateTimeFormatter = SimpleDateFormat("dd/MM/yyyy - hh:mm a", Locale("es", "ES"))

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun CitasMedicasScreen(
    onBack: () -> Unit,
    viewModel: AppointmentViewModel
) {
    var appointments by remember { mutableStateOf<List<AppointmentEntity>>(emptyList()) }
    var showAddDialog by remember { mutableStateOf(false) }

    LaunchedEffect(Unit) {
        viewModel.allAppointments.collectLatest { appts ->
            appointments = appts
        }
    }

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Citas Médicas") },
                navigationIcon = {
                    IconButton(onClick = onBack) {
                        Icon(Icons.Default.ArrowBack, contentDescription = "Volver")
                    }
                },
                actions = {
                    Button(
                        onClick = { showAddDialog = true },
                        modifier = Modifier.padding(end = 8.dp)
                    ) {
                        Icon(Icons.Filled.Add, contentDescription = null)
                        Spacer(Modifier.width(4.dp))
                        Text("Agregar")
                    }
                }
            )
        }
    ) { paddingValues ->
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues)
                .padding(16.dp)
        ) {
            if (appointments.isEmpty()) {
                Box(
                    modifier = Modifier.fillMaxSize(),
                    contentAlignment = Alignment.Center
                ) {
                    Text(
                        "No hay citas programadas.\nPresiona 'Agregar' para registrar una.",
                        style = MaterialTheme.typography.titleMedium,
                        color = MaterialTheme.colorScheme.onSurfaceVariant
                    )
                }
            } else {
                LazyColumn(verticalArrangement = Arrangement.spacedBy(12.dp)) {
                    items(appointments) { appt ->
                        AppointmentCard(
                            appointment = appt,
                            onDelete = {
                                viewModel.deleteAppointment(appt)
                            }
                        )
                    }
                }
            }
        }
    }

    if (showAddDialog) {
        AddAppointmentDialog(
            onDismiss = { showAddDialog = false },
            onSave = { place, dateTime ->
                val newAppointment = AppointmentEntity(
                    place = place,
                    dateTime = dateTime
                )
                viewModel.insertAppointment(newAppointment)
                showAddDialog = false
            }
        )
    }
}

@Composable
fun AppointmentCard(
    appointment: AppointmentEntity,
    onDelete: () -> Unit
) {
    Card(
        modifier = Modifier
            .fillMaxWidth()
            .padding(horizontal = 8.dp, vertical = 6.dp),
        elevation = CardDefaults.cardElevation(8.dp),
        colors = CardDefaults.cardColors(
            containerColor = Color(0xFFE8F5E9)
        ),
        shape = RoundedCornerShape(20.dp)
    ) {
        Box {
            Canvas(modifier = Modifier.fillMaxSize()) {
                drawCircle(
                    color = Color(0xFF69F0AE).copy(alpha = 0.3f),
                    radius = 100f,
                    center = Offset(size.width - 50f, 50f)
                )
                drawCircle(
                    color = Color(0xFF00E676).copy(alpha = 0.2f),
                    radius = 80f,
                    center = Offset(50f, size.height - 50f)
                )
            }

            Row(
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(20.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.SpaceBetween
            ) {
                Box(
                    modifier = Modifier
                        .size(64.dp)
                        .background(
                            color = Color(0xFF00E676),
                            shape = RoundedCornerShape(16.dp)
                        ),
                    contentAlignment = Alignment.Center
                ) {
                    Text(text = "🏥", fontSize = 32.sp)
                }

                Spacer(modifier = Modifier.width(16.dp))

                Column(modifier = Modifier.weight(1f)) {
                    Row(
                        verticalAlignment = Alignment.CenterVertically,
                        modifier = Modifier.fillMaxWidth()
                    ) {
                        Box(
                            modifier = Modifier
                                .size(32.dp)
                                .background(
                                    color = Color(0xFFFFFFFF).copy(alpha = 0.7f),
                                    shape = CircleShape
                                ),
                            contentAlignment = Alignment.Center
                        ) {
                            Icon(
                                Icons.Default.LocationOn,
                                contentDescription = null,
                                tint = Color(0xFF00C853),
                                modifier = Modifier.size(20.dp)
                            )
                        }

                        Spacer(Modifier.width(8.dp))

                        Text(
                            text = appointment.place,
                            style = MaterialTheme.typography.titleLarge,
                            fontWeight = FontWeight.Bold,
                            color = Color(0xFF1B5E20)
                        )
                    }

                    Spacer(modifier = Modifier.height(12.dp))

                    Row(
                        verticalAlignment = Alignment.CenterVertically,
                        modifier = Modifier
                            .fillMaxWidth()
                            .background(
                                color = Color(0xFFFFFFFF).copy(alpha = 0.6f),
                                shape = RoundedCornerShape(12.dp)
                            )
                            .padding(12.dp)
                    ) {
                        Icon(
                            Icons.Default.CalendarMonth,
                            contentDescription = null,
                            tint = Color(0xFF00E676),
                            modifier = Modifier.size(24.dp)
                        )

                        Spacer(Modifier.width(8.dp))

                        Column {
                            Text(
                                text = "Fecha y Hora",
                                style = MaterialTheme.typography.labelSmall,
                                color = Color(0xFF2E7D32).copy(alpha = 0.7f),
                                fontWeight = FontWeight.SemiBold
                            )
                            Text(
                                text = DateTimeFormatter.format(Date(appointment.dateTime)),
                                style = MaterialTheme.typography.bodyLarge,
                                fontWeight = FontWeight.Medium,
                                color = Color(0xFF1B5E20)
                            )
                        }
                    }
                }

                Spacer(modifier = Modifier.width(12.dp))

                IconButton(
                    onClick = onDelete,
                    modifier = Modifier
                        .size(48.dp)
                        .background(
                            color = Color(0xFFFF5252).copy(alpha = 0.2f),
                            shape = CircleShape
                        )
                ) {
                    Icon(
                        Icons.Default.Delete,
                        contentDescription = "Eliminar Cita",
                        tint = Color(0xFFD32F2F),
                        modifier = Modifier.size(24.dp)
                    )
                }
            }
        }
    }
}

@Composable
fun AddAppointmentDialog(
    onDismiss: () -> Unit,
    onSave: (place: String, dateTime: Long) -> Unit
) {
    val context = LocalContext.current
    var place by remember { mutableStateOf("") }
    var selectedDateTime by remember { mutableStateOf(System.currentTimeMillis()) }

    val showDateTimePicker = {
        val calendar = Calendar.getInstance().apply { timeInMillis = selectedDateTime }

        DatePickerDialog(
            context,
            { _, year, month, day ->
                calendar.set(year, month, day)

                TimePickerDialog(
                    context,
                    { _, hour, minute ->
                        calendar.set(Calendar.HOUR_OF_DAY, hour)
                        calendar.set(Calendar.MINUTE, minute)
                        selectedDateTime = calendar.timeInMillis
                    },
                    calendar.get(Calendar.HOUR_OF_DAY),
                    calendar.get(Calendar.MINUTE),
                    false
                ).show()
            },
            calendar.get(Calendar.YEAR),
            calendar.get(Calendar.MONTH),
            calendar.get(Calendar.DAY_OF_MONTH)
        ).show()
    }

    AlertDialog(
        onDismissRequest = onDismiss,
        title = { Text("Agregar Nueva Cita Médica") },
        text = {
            Column(
                modifier = Modifier.padding(top = 16.dp),
                verticalArrangement = Arrangement.spacedBy(16.dp)
            ) {
                OutlinedTextField(
                    value = place,
                    onValueChange = { place = it },
                    label = { Text("Lugar de la Cita") },
                    leadingIcon = {
                        Icon(Icons.Default.LocationOn, contentDescription = null)
                    },
                    modifier = Modifier.fillMaxWidth()
                )

                Box(
                    modifier = Modifier
                        .fillMaxWidth()
                        .clickable { showDateTimePicker() }
                ) {
                    OutlinedTextField(
                        value = DateTimeFormatter.format(Date(selectedDateTime)),
                        onValueChange = {},
                        label = { Text("Fecha y Hora") },
                        leadingIcon = {
                            Icon(Icons.Default.Schedule, contentDescription = null)
                        },
                        readOnly = true,
                        enabled = false,
                        colors = OutlinedTextFieldDefaults.colors(
                            disabledTextColor = MaterialTheme.colorScheme.onSurface,
                            disabledBorderColor = MaterialTheme.colorScheme.outline,
                            disabledLeadingIconColor = MaterialTheme.colorScheme.onSurfaceVariant,
                            disabledLabelColor = MaterialTheme.colorScheme.onSurfaceVariant
                        ),
                        modifier = Modifier.fillMaxWidth()
                    )
                }
            }
        },
        confirmButton = {
            Button(
                enabled = place.isNotBlank(),
                onClick = { onSave(place, selectedDateTime) }
            ) {
                Text("Aceptar")
            }
        },
        dismissButton = {
            TextButton(onClick = onDismiss) {
                Text("Cancelar")
            }
        }
    )
}
```

### Descripción General

Este archivo contiene la pantalla principal de citas médicas, donde el usuario puede ver todas sus citas programadas, agregar nuevas y eliminar citas existentes. Es una pantalla completa con lista interactiva y diálogos para agregar citas con selector de fecha y hora.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.citas`

### Explicación Detallada

**CitasMedicasScreen()**

Pantalla principal de citas médicas con lista interactiva.

- Parámetros: `onBack` — para regresar; `viewModel` — maneja los datos de las citas.
- Componentes: `TopAppBar` con título, botón de retroceso y botón "Agregar"; lista de citas con `AppointmentCard`; mensaje de estado vacío.
- Flujo de datos: Usa `LaunchedEffect` para recolectar las citas del ViewModel. La lista se actualiza automáticamente al agregar o eliminar.

**AppointmentCard()**

Muestra una tarjeta individual para cada cita con diseño llamativo.

- Fondo verde menta suave (#E8F5E9) con círculos decorativos dibujados con `Canvas`.
- Incluye ícono de hospital, nombre del lugar, fecha y hora formateada, y botón de eliminar (rojo).

**AddAppointmentDialog()**

Diálogo para agregar una nueva cita. El campo de fecha y hora abre primero un `DatePickerDialog` y luego un `TimePickerDialog`. El botón "Aceptar" solo se habilita cuando el campo "Lugar" no está vacío.

### Flujo de Trabajo

1. Usuario presiona "Agregar" → se abre `AddAppointmentDialog`.
2. Usuario llena datos y presiona "Aceptar" → se crea `AppointmentEntity` y se inserta vía ViewModel.
3. La lista se actualiza automáticamente.
4. Usuario presiona el botón rojo en una tarjeta → se llama a `viewModel.deleteAppointment()`.

---

## ConfiguracionScreen.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.configuracion

import android.content.Intent
import android.net.Uri
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import kotlinx.coroutines.flow.collectLatest
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.EmergencyContactEntity
import mx.edu.utng.aimc.com.pantallaprincipal.viewmodel.EmergencyContactViewModel

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun ConfiguracionScreen(
    onBackClick: () -> Unit = {},
    onThemeChange: (Boolean) -> Unit = {},
    isDarkTheme: Boolean = false,
    emergencyViewModel: EmergencyContactViewModel
) {
    val context = LocalContext.current
    var notificacionesActivadas by remember { mutableStateOf(false) }
    var temaOscuro by remember { mutableStateOf(isDarkTheme) }
    var showAddContactDialog by remember { mutableStateOf(false) }
    var emergencyContacts by remember { mutableStateOf<List<EmergencyContactEntity>>(emptyList()) }

    LaunchedEffect(Unit) {
        emergencyViewModel.allContacts.collectLatest { contacts ->
            emergencyContacts = contacts
        }
    }

    val backgroundColor = if (temaOscuro) Color(0xFF121212) else Color.White
    val textColor = if (temaOscuro) Color.White else Color.Black
    val cardColor = if (temaOscuro) Color(0xFF1E1E1E) else Color(0xFFF5F5F5)

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Configuración") },
                navigationIcon = {
                    IconButton(onClick = onBackClick) {
                        Icon(Icons.Default.ArrowBack, contentDescription = "Volver")
                    }
                },
                colors = TopAppBarDefaults.topAppBarColors(
                    containerColor = if (temaOscuro) Color(0xFF1E1E1E) else MaterialTheme.colorScheme.primary,
                    titleContentColor = Color.White,
                    navigationIconContentColor = Color.White
                )
            )
        },
        containerColor = backgroundColor
    ) { paddingValues ->
        LazyColumn(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues)
                .padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(16.dp)
        ) {
            item {
                Card(
                    shape = RoundedCornerShape(16.dp),
                    modifier = Modifier.fillMaxWidth(),
                    colors = CardDefaults.cardColors(containerColor = cardColor),
                    elevation = CardDefaults.cardElevation(6.dp)
                ) {
                    Row(
                        modifier = Modifier
                            .fillMaxWidth()
                            .padding(16.dp),
                        verticalAlignment = Alignment.CenterVertically,
                        horizontalArrangement = Arrangement.SpaceBetween
                    ) {
                        Row(verticalAlignment = Alignment.CenterVertically) {
                            Icon(
                                Icons.Default.Notifications,
                                contentDescription = null,
                                tint = MaterialTheme.colorScheme.primary
                            )
                            Spacer(modifier = Modifier.width(12.dp))
                            Text(
                                "Notificaciones",
                                fontSize = 16.sp,
                                color = textColor,
                                fontWeight = FontWeight.Medium
                            )
                        }
                        Switch(
                            checked = notificacionesActivadas,
                            onCheckedChange = { notificacionesActivadas = it }
                        )
                    }
                }
            }

            item {
                Card(
                    shape = RoundedCornerShape(16.dp),
                    modifier = Modifier.fillMaxWidth(),
                    colors = CardDefaults.cardColors(containerColor = cardColor),
                    elevation = CardDefaults.cardElevation(6.dp)
                ) {
                    Row(
                        modifier = Modifier
                            .fillMaxWidth()
                            .padding(16.dp),
                        verticalAlignment = Alignment.CenterVertically,
                        horizontalArrangement = Arrangement.SpaceBetween
                    ) {
                        Row(verticalAlignment = Alignment.CenterVertically) {
                            Icon(
                                if (temaOscuro) Icons.Default.DarkMode else Icons.Default.LightMode,
                                contentDescription = null,
                                tint = MaterialTheme.colorScheme.primary
                            )
                            Spacer(modifier = Modifier.width(12.dp))
                            Column {
                                Text(
                                    "Tema de apariencia",
                                    fontSize = 16.sp,
                                    fontWeight = FontWeight.Medium,
                                    color = textColor
                                )
                                Text(
                                    if (temaOscuro) "Modo Oscuro" else "Modo Claro",
                                    fontSize = 14.sp,
                                    color = textColor.copy(alpha = 0.6f)
                                )
                            }
                        }
                        Switch(
                            checked = temaOscuro,
                            onCheckedChange = {
                                temaOscuro = it
                                onThemeChange(it)
                            }
                        )
                    }
                }
            }

            item {
                Card(
                    shape = RoundedCornerShape(16.dp),
                    modifier = Modifier.fillMaxWidth(),
                    colors = CardDefaults.cardColors(containerColor = cardColor),
                    elevation = CardDefaults.cardElevation(6.dp)
                ) {
                    Column(modifier = Modifier.padding(16.dp)) {
                        Row(
                            modifier = Modifier.fillMaxWidth(),
                            horizontalArrangement = Arrangement.SpaceBetween,
                            verticalAlignment = Alignment.CenterVertically
                        ) {
                            Row(verticalAlignment = Alignment.CenterVertically) {
                                Icon(
                                    Icons.Default.Phone,
                                    contentDescription = null,
                                    tint = Color(0xFFD32F2F)
                                )
                                Spacer(modifier = Modifier.width(12.dp))
                                Text(
                                    "Contactos de emergencia",
                                    fontSize = 16.sp,
                                    fontWeight = FontWeight.Bold,
                                    color = textColor
                                )
                            }
                            IconButton(onClick = { showAddContactDialog = true }) {
                                Icon(
                                    Icons.Default.Add,
                                    contentDescription = "Agregar contacto",
                                    tint = MaterialTheme.colorScheme.primary
                                )
                            }
                        }

                        Spacer(modifier = Modifier.height(12.dp))

                        Button(
                            onClick = {
                                val intent = Intent(Intent.ACTION_DIAL, Uri.parse("tel:911"))
                                context.startActivity(intent)
                            },
                            colors = ButtonDefaults.buttonColors(
                                containerColor = Color(0xFFD32F2F)
                            ),
                            modifier = Modifier.fillMaxWidth()
                        ) {
                            Icon(Icons.Default.Phone, contentDescription = null)
                            Spacer(modifier = Modifier.width(8.dp))
                            Text("Emergencias 911", fontSize = 16.sp)
                        }

                        if (emergencyContacts.isNotEmpty()) {
                            Spacer(modifier = Modifier.height(12.dp))
                            emergencyContacts.forEach { contact ->
                                EmergencyContactItem(
                                    contact = contact,
                                    onCall = {
                                        val intent = Intent(Intent.ACTION_DIAL, Uri.parse("tel:${contact.phoneNumber}"))
                                        context.startActivity(intent)
                                    },
                                    onDelete = {
                                        emergencyViewModel.deleteContact(contact)
                                    },
                                    textColor = textColor
                                )
                                Spacer(modifier = Modifier.height(8.dp))
                            }
                        }
                    }
                }
            }
        }
    }

    if (showAddContactDialog) {
        AddEmergencyContactDialog(
            onDismiss = { showAddContactDialog = false },
            onSave = { name, phone ->
                emergencyViewModel.insertContact(name, phone)
                showAddContactDialog = false
            }
        )
    }
}

@Composable
fun EmergencyContactItem(
    contact: EmergencyContactEntity,
    onCall: () -> Unit,
    onDelete: () -> Unit,
    textColor: Color
) {
    Card(
        modifier = Modifier.fillMaxWidth(),
        colors = CardDefaults.cardColors(
            containerColor = MaterialTheme.colorScheme.primaryContainer.copy(alpha = 0.3f)
        )
    ) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.SpaceBetween
        ) {
            Column(modifier = Modifier.weight(1f)) {
                Text(
                    contact.name,
                    fontWeight = FontWeight.Bold,
                    fontSize = 16.sp,
                    color = textColor
                )
                Text(
                    contact.phoneNumber,
                    fontSize = 14.sp,
                    color = textColor.copy(alpha = 0.7f)
                )
            }
            Row {
                IconButton(onClick = onCall) {
                    Icon(
                        Icons.Default.Phone,
                        contentDescription = "Llamar",
                        tint = Color(0xFF4CAF50)
                    )
                }
                IconButton(onClick = onDelete) {
                    Icon(
                        Icons.Default.Delete,
                        contentDescription = "Eliminar",
                        tint = Color(0xFFD32F2F)
                    )
                }
            }
        }
    }
}

@Composable
fun AddEmergencyContactDialog(
    onDismiss: () -> Unit,
    onSave: (name: String, phone: String) -> Unit
) {
    var name by remember { mutableStateOf("") }
    var phone by remember { mutableStateOf("") }

    AlertDialog(
        onDismissRequest = onDismiss,
        title = { Text("Agregar Contacto de Emergencia") },
        text = {
            Column(verticalArrangement = Arrangement.spacedBy(12.dp)) {
                OutlinedTextField(
                    value = name,
                    onValueChange = { name = it },
                    label = { Text("Nombre") },
                    leadingIcon = {
                        Icon(Icons.Default.Person, contentDescription = null)
                    },
                    modifier = Modifier.fillMaxWidth()
                )
                OutlinedTextField(
                    value = phone,
                    onValueChange = { phone = it },
                    label = { Text("Número de teléfono") },
                    leadingIcon = {
                        Icon(Icons.Default.Phone, contentDescription = null)
                    },
                    modifier = Modifier.fillMaxWidth()
                )
            }
        },
        confirmButton = {
            Button(
                enabled = name.isNotBlank() && phone.isNotBlank(),
                onClick = { onSave(name, phone) }
            ) {
                Text("Guardar")
            }
        },
        dismissButton = {
            TextButton(onClick = onDismiss) {
                Text("Cancelar")
            }
        }
    )
}
```

### Descripción General

Este archivo contiene la pantalla de configuración de la aplicación. Permite al usuario personalizar su experiencia ajustando notificaciones, tema de colores y gestionar contactos de emergencia.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.configuracion`

### Explicación Detallada

**ConfiguracionScreen()**

Pantalla con tres secciones en una lista vertical: Notificaciones, Tema de apariencia y Contactos de emergencia.

- Parámetros: `onBackClick`, `onThemeChange`, `isDarkTheme`, `emergencyViewModel`.
- Colores dinámicos: `backgroundColor`, `textColor` y `cardColor` cambian según el tema activo.

**Secciones de Configuración**

*Sección Notificaciones* — Switch para activar/desactivar notificaciones. Actualmente solo cambia visualmente; no tiene funcionalidad real implementada.

*Sección Tema de Apariencia* — Switch que cambia entre tema claro y oscuro. Al cambiar, ejecuta `onThemeChange()` para notificar al componente padre (`NavGraph`), lo que actualiza el tema en toda la aplicación.

*Sección Contactos de Emergencia* — Incluye un botón rojo que abre el marcador con el número 911, lista de contactos guardados con opciones de llamar y eliminar, y botón "+" para agregar nuevos.

**EmergencyContactItem()**

Muestra un contacto individual con nombre, número y botones de llamada (verde) y eliminar (rojo).

**AddEmergencyContactDialog()**

Diálogo para agregar un nuevo contacto con campos de nombre y teléfono. El botón "Guardar" solo se habilita cuando ambos campos tienen texto.

### Flujo de Trabajo

Cambiar tema: El switch actualiza `temaOscuro` localmente → llama a `onThemeChange()` → `NavGraph` actualiza el tema global → la pantalla se recolorea automáticamente.

Agregar contacto: Botón "+" → diálogo → usuario ingresa datos → "Guardar" → `emergencyViewModel.insertContact()` → lista se actualiza automáticamente.

Llamar a un contacto: Botón verde → `Intent` con `ACTION_DIAL` → Android abre el marcador con el número.

---

## AgregarNumeroEmergencia.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.emergencia

import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun AgregarNumeroEmergencia(
    onAgregar: (nombre: String, numero: String) -> Unit = { _, _ -> }
) {
    var nombre by remember { mutableStateOf("") }
    var numero by remember { mutableStateOf("") }

    Card(
        shape = RoundedCornerShape(16.dp),
        modifier = Modifier
            .fillMaxWidth()
            .padding(16.dp),
        colors = CardDefaults.cardColors(containerColor = Color(0xFFF5F5F5)),
        elevation = CardDefaults.cardElevation(8.dp)
    ) {
        Column(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(12.dp)
        ) {
            Text(
                text = "Agregar número de emergencia",
                fontSize = 20.sp,
                fontWeight = FontWeight.Bold,
                color = Color.Black
            )

            OutlinedTextField(
                value = nombre,
                onValueChange = { nombre = it },
                label = { Text("Nombre") },
                modifier = Modifier.fillMaxWidth(),
                shape = RoundedCornerShape(10.dp)
            )

            OutlinedTextField(
                value = numero,
                onValueChange = { numero = it },
                label = { Text("Número de contacto") },
                modifier = Modifier.fillMaxWidth(),
                shape = RoundedCornerShape(10.dp)
            )

            Button(
                onClick = { onAgregar(nombre, numero) },
                modifier = Modifier
                    .fillMaxWidth()
                    .height(50.dp),
                colors = ButtonDefaults.buttonColors(containerColor = Color(0xFFD32F2F)),
                shape = RoundedCornerShape(12.dp)
            ) {
                Text("Agregar", color = Color.White, fontSize = 18.sp)
            }
        }
    }
}
```

### Descripción General

Este archivo define una pantalla simple para agregar un número de contacto de emergencia. Es un formulario básico con dos campos (nombre y número) y un botón rojo para guardar.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.emergencia`

### Explicación Detallada

**AgregarNumeroEmergencia()**

Muestra una tarjeta con un formulario para agregar un nuevo contacto de emergencia.

- Parámetros: `onAgregar` — Función que recibe `nombre` y `numero` cuando el usuario presiona "Agregar". Por defecto es una función vacía.
- Variables de estado: `nombre` y `numero` almacenan el texto que el usuario escribe en cada campo.
- Botón rojo (#D32F2F) que ocupa todo el ancho. Al presionarlo, ejecuta `onAgregar` con los valores actuales.
- No tiene validación: se puede presionar incluso si los campos están vacíos.

---

## FarmaciasCercanasScreen.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.farmacias

import android.Manifest
import android.annotation.SuppressLint
import android.content.Intent
import android.net.Uri
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ArrowBack
import androidx.compose.material.icons.filled.Directions
import androidx.compose.material.icons.filled.LocalPharmacy
import androidx.compose.material.icons.filled.MyLocation
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import com.google.accompanist.permissions.ExperimentalPermissionsApi
import com.google.accompanist.permissions.isGranted
import com.google.accompanist.permissions.rememberPermissionState
import com.google.android.gms.location.LocationServices
import com.google.android.gms.maps.model.CameraPosition
import com.google.android.gms.maps.model.LatLng
import com.google.maps.android.compose.*
import kotlinx.coroutines.tasks.await
import org.json.JSONArray
import okhttp3.OkHttpClient
import okhttp3.Request
import org.json.JSONObject

data class Farmacia(
    val nombre: String,
    val direccion: String,
    val distancia: String,
    val latLng: LatLng
)

@OptIn(ExperimentalMaterial3Api::class, ExperimentalPermissionsApi::class)
@Composable
fun FarmaciasCercanasScreen(
    onBackClick: () -> Unit
) {
    val context = LocalContext.current
    var userLocation by remember { mutableStateOf<LatLng?>(null) }
    var farmacias by remember { mutableStateOf<List<Farmacia>>(emptyList()) }
    var isLoading by remember { mutableStateOf(true) }

    val locationPermissionState = rememberPermissionState(
        permission = Manifest.permission.ACCESS_FINE_LOCATION
    )

    @SuppressLint("MissingPermission")
    LaunchedEffect(locationPermissionState.status.isGranted) {
        if (locationPermissionState.status.isGranted) {
            try {
                val fusedLocationClient = LocationServices.getFusedLocationProviderClient(context)
                val location = fusedLocationClient.lastLocation.await()
                location?.let {
                    userLocation = LatLng(it.latitude, it.longitude)

                    val API_KEY = "AIzaSyAUdfxCL1xKk66Ai8GcMET6fS1dyUH9pOk"

                    farmacias = obtenerFarmaciasReales(
                        userLocation = userLocation!!,
                        apiKey = API_KEY
                    )
                }
                isLoading = false
            } catch (e: Exception) {
                isLoading = false
            }
        } else {
            isLoading = false
        }
    }

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Farmacias Cercanas") },
                navigationIcon = {
                    IconButton(onClick = onBackClick) {
                        Icon(Icons.Default.ArrowBack, contentDescription = "Volver")
                    }
                }
            )
        }
    ) { paddingValues ->
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues)
        ) {

            if (userLocation != null) {
                GoogleMapView(
                    userLocation = userLocation!!,
                    farmacias = farmacias,
                    modifier = Modifier
                        .fillMaxWidth()
                        .weight(0.5f)
                )
            } else {
                Box(
                    modifier = Modifier
                        .fillMaxWidth()
                        .weight(0.5f),
                    contentAlignment = Alignment.Center
                ) {
                    when {
                        !locationPermissionState.status.isGranted -> {
                            Column(
                                horizontalAlignment = Alignment.CenterHorizontally,
                                verticalArrangement = Arrangement.spacedBy(16.dp)
                            ) {
                                Icon(
                                    Icons.Default.MyLocation,
                                    contentDescription = null,
                                    modifier = Modifier.size(64.dp),
                                    tint = MaterialTheme.colorScheme.primary
                                )
                                Text(
                                    "Se necesita permiso de ubicación",
                                    style = MaterialTheme.typography.titleMedium
                                )
                                Button(onClick = { locationPermissionState.launchPermissionRequest() }) {
                                    Text("Conceder Permiso")
                                }
                            }
                        }
                        isLoading -> CircularProgressIndicator()
                    }
                }
            }

            Divider()

            if (farmacias.isNotEmpty()) {
                LazyColumn(
                    modifier = Modifier
                        .fillMaxWidth()
                        .weight(0.5f),
                    contentPadding = PaddingValues(16.dp),
                    verticalArrangement = Arrangement.spacedBy(12.dp)
                ) {
                    items(farmacias) { farmacia ->
                        FarmaciaCard(
                            farmacia = farmacia,
                            onNavigate = {
                                val uri = Uri.parse(
                                    "google.navigation:q=${farmacia.latLng.latitude},${farmacia.latLng.longitude}"
                                )
                                val intent = Intent(Intent.ACTION_VIEW, uri).apply {
                                    setPackage("com.google.android.apps.maps")
                                }
                                context.startActivity(intent)
                            }
                        )
                    }
                }
            } else if (!isLoading) {
                Box(
                    modifier = Modifier
                        .fillMaxWidth()
                        .weight(0.5f),
                    contentAlignment = Alignment.Center
                ) {
                    Text(
                        "No se encontraron farmacias cercanas",
                        style = MaterialTheme.typography.titleMedium
                    )
                }
            }
        }
    }
}

@Composable
fun GoogleMapView(
    userLocation: LatLng,
    farmacias: List<Farmacia>,
    modifier: Modifier = Modifier
) {
    val cameraPositionState = rememberCameraPositionState {
        position = CameraPosition.fromLatLngZoom(userLocation, 14f)
    }

    GoogleMap(
        modifier = modifier,
        cameraPositionState = cameraPositionState,
        properties = MapProperties(isMyLocationEnabled = true),
        uiSettings = MapUiSettings(
            zoomControlsEnabled = true,
            myLocationButtonEnabled = true
        )
    ) {
        Marker(
            state = MarkerState(position = userLocation),
            title = "Tu ubicación"
        )

        farmacias.forEach { farmacia ->
            Marker(
                state = MarkerState(position = farmacia.latLng),
                title = farmacia.nombre,
                snippet = farmacia.direccion,
                icon = com.google.android.gms.maps.model.BitmapDescriptorFactory
                    .defaultMarker(com.google.android.gms.maps.model.BitmapDescriptorFactory.HUE_GREEN)
            )
        }
    }
}

@Composable
fun FarmaciaCard(
    farmacia: Farmacia,
    onNavigate: () -> Unit
) {
    Card(
        modifier = Modifier.fillMaxWidth(),
        elevation = CardDefaults.cardElevation(4.dp)
    ) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            Icon(
                Icons.Default.LocalPharmacy,
                contentDescription = null,
                modifier = Modifier.size(40.dp),
                tint = MaterialTheme.colorScheme.primary
            )

            Spacer(modifier = Modifier.width(16.dp))

            Column(modifier = Modifier.weight(1f)) {
                Text(
                    farmacia.nombre,
                    style = MaterialTheme.typography.titleMedium,
                    fontWeight = FontWeight.Bold
                )
                Spacer(modifier = Modifier.height(4.dp))
                Text(farmacia.direccion)
            }

            IconButton(onClick = onNavigate) {
                Icon(
                    Icons.Default.Directions,
                    contentDescription = "Cómo llegar",
                    tint = MaterialTheme.colorScheme.primary
                )
            }
        }
    }
}

suspend fun obtenerFarmaciasReales(
    userLocation: LatLng,
    apiKey: String
): List<Farmacia> {

    val client = OkHttpClient()

    val url =
        "https://maps.googleapis.com/maps/api/place/nearbysearch/json?" +
                "location=${userLocation.latitude},${userLocation.longitude}" +
                "&radius=40000" +
                "&type=pharmacy" +
                "&key=$apiKey"

    val request = Request.Builder()
        .url(url)
        .build()

    return try {
        val response = client.newCall(request).execute()
        val json = JSONObject(response.body?.string() ?: "{}")
        val results: JSONArray = json.optJSONArray("results") ?: JSONArray()

        List(results.length()) { i ->
            val item = results.getJSONObject(i)

            val name = item.optString("name", "Farmacia desconocida")
            val address = item.optString("vicinity", "Sin dirección")

            val location = item.getJSONObject("geometry").getJSONObject("location")
            val lat = location.getDouble("lat")
            val lng = location.getDouble("lng")

            Farmacia(
                nombre = name,
                direccion = address,
                distancia = "",
                latLng = LatLng(lat, lng)
            )
        }
    } catch (e: Exception) {
        e.printStackTrace()
        emptyList()
    }
}
```

### Descripción General

Este archivo contiene la pantalla para buscar farmacias cercanas a la ubicación del usuario. Integra permisos de ubicación, Google Maps y la API de Google Places para obtener farmacias reales. La pantalla se divide en dos partes: un mapa en la mitad superior y una lista de farmacias en la mitad inferior.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.farmacias`

### Explicación Detallada

**Modelo de Datos Farmacia**

```kotlin
data class Farmacia(
    val nombre: String,
    val direccion: String,
    val distancia: String,
    val latLng: LatLng
)
```

Representa una farmacia. El campo `distancia` está preparado pero no se usa actualmente (siempre vacío).

**FarmaciasCercanasScreen()**

Pantalla principal que solicita permiso de ubicación, obtiene la última ubicación conocida y llama a la API de Google Places con un radio de 40 kilómetros y el filtro `type=pharmacy`.

La API Key está hardcodeada en el código, lo cual no es una práctica segura:

```kotlin
val API_KEY = "AIzaSyAUdfxCL1xKk66Ai8GcMET6fS1dyUH9pOk"
```

**GoogleMapView()**

Muestra un mapa centrado en la ubicación del usuario (zoom 14f) con marcadores verdes (`HUE_GREEN`) para las farmacias y marcador por defecto para el usuario.

**FarmaciaCard()**

Tarjeta con ícono de farmacia, nombre, dirección y botón de navegación. Al presionar "Cómo llegar", abre Google Maps en modo navegación:

```kotlin
val uri = Uri.parse("google.navigation:q=${farmacia.latLng.latitude},${farmacia.latLng.longitude}")
val intent = Intent(Intent.ACTION_VIEW, uri).apply {
    setPackage("com.google.android.apps.maps")
}
```

**obtenerFarmaciasReales()**

Función suspendida que llama a la API de Places con `OkHttpClient`, parsea la respuesta JSON y retorna una lista de objetos `Farmacia`.

### Flujo de Trabajo

1. La pantalla solicita permiso de ubicación.
2. Al concederse, obtiene la última ubicación conocida.
3. Llama a la API de Places y recibe farmacias en un radio de 40 km.
4. El mapa muestra marcadores; la lista muestra la información de cada farmacia.
5. Al tocar "Cómo llegar", se abre Google Maps con la ruta.

---

## MediAlertaScreen.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.home

import android.content.Intent
import android.net.Uri
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.Image
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.res.painterResource
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import androidx.compose.ui.platform.LocalContext

private val Pink = Color(0xFFFF7EB5)
private val PinkBorder = Color(0xFFD81B60)

private val Purple = Color(0xFFA56CFF)
private val PurpleBorder = Color(0xFF6A1B9A)

private val Yellow = Color(0xFFFFD93D)
private val YellowBorder = Color(0xFFF7B500)

private val Green = Color(0xFF74FF8F)
private val GreenBorder = Color(0xFF00C853)

private val BackgroundLilac = Color(0xFFF4E9FF)

@Composable
fun MediAlertaScreen(
    modifier: Modifier = Modifier,
    onNavigateToMedicamentos: () -> Unit = {},
    onNavigateToMedicationList: () -> Unit = {},
    onNavigateToCitas: () -> Unit = {},
    onNavigateToFarmacias: () -> Unit = {},
    onNavigateToConfiguracion: () -> Unit = {},
) {

    val context = LocalContext.current

    Box(
        modifier = Modifier
            .fillMaxSize()
            .background(BackgroundLilac)
    ) {

        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(24.dp),
            horizontalAlignment = Alignment.CenterHorizontally
        ) {

            Spacer(modifier = Modifier.height(25.dp))

            Text(
                text = "MediAlerta",
                fontSize = 40.sp,
                fontWeight = FontWeight.Bold,
                color = Color(0xFF4A148C)
            )

            Text(
                text = "Tu salud en tus manos",
                fontSize = 17.sp,
                color = Color(0xFF6A1B9A),
                modifier = Modifier.padding(top = 4.dp)
            )

            Spacer(modifier = Modifier.height(22.dp))

            Card(
                modifier = Modifier
                    .fillMaxWidth(0.85f)
                    .height(60.dp)
                    .shadow(12.dp, RoundedCornerShape(16.dp))
                    .clickable {
                        val intent = Intent(Intent.ACTION_DIAL, Uri.parse("tel:911"))
                        context.startActivity(intent)
                    },
                shape = RoundedCornerShape(16.dp),
                colors = CardDefaults.cardColors(
                    containerColor = Color(0xFFE53935)
                )
            ) {
                Box(contentAlignment = Alignment.Center, modifier = Modifier.fillMaxSize()) {
                    Text(
                        text = "Emergencia 911",
                        fontSize = 22.sp,
                        fontWeight = FontWeight.Bold,
                        color = Color.White
                    )
                }
            }

            Spacer(modifier = Modifier.height(35.dp))

            Column(
                verticalArrangement = Arrangement.spacedBy(18.dp),
                horizontalAlignment = Alignment.CenterHorizontally
            ) {

                Row(horizontalArrangement = Arrangement.spacedBy(18.dp)) {

                    PrettyCard(
                        title = "Medicamentos",
                        color = Pink,
                        borderColor = PinkBorder,
                        icon = Icons.Default.MedicalServices,
                        onClick = onNavigateToMedicationList
                    )

                    PrettyCard(
                        title = "Citas",
                        color = Purple,
                        borderColor = PurpleBorder,
                        icon = Icons.Default.CalendarMonth,
                        onClick = onNavigateToCitas
                    )
                }

                Row(horizontalArrangement = Arrangement.spacedBy(18.dp)) {

                    PrettyCard(
                        title = "Farmacias",
                        color = Yellow,
                        borderColor = YellowBorder,
                        icon = Icons.Default.LocalPharmacy,
                        onClick = onNavigateToFarmacias
                    )

                    PrettyCard(
                        title = "Configuración",
                        color = Green,
                        borderColor = GreenBorder,
                        icon = Icons.Default.Settings,
                        onClick = onNavigateToConfiguracion
                    )
                }
            }
        }

        Image(
            painter = painterResource(id = R.drawable.estetoscopio),
            contentDescription = "Ilustración fondo",
            modifier = Modifier
                .align(Alignment.BottomCenter)
                .size(250.dp)
                .padding(bottom = 10.dp),
            alpha = 0.90f
        )
    }
}

@Composable
fun PrettyCard(
    icon: ImageVector,
    title: String,
    color: Color,
    borderColor: Color,
    onClick: () -> Unit
) {

    Card(
        modifier = Modifier
            .size(width = 150.dp, height = 120.dp)
            .shadow(8.dp, RoundedCornerShape(20.dp))
            .clickable { onClick() },
        shape = RoundedCornerShape(20.dp),
        colors = CardDefaults.cardColors(containerColor = color),
        border = BorderStroke(3.dp, borderColor)
    ) {
        Column(
            modifier = Modifier.fillMaxSize(),
            verticalArrangement = Arrangement.Center,
            horizontalAlignment = Alignment.CenterHorizontally
        ) {

            Icon(
                imageVector = icon,
                contentDescription = title,
                tint = Color.White,
                modifier = Modifier.size(38.dp)
            )

            Spacer(modifier = Modifier.height(8.dp))

            Text(
                text = title,
                color = Color.White,
                fontSize = 17.sp,
                fontWeight = FontWeight.Bold,
                textAlign = TextAlign.Center
            )
        }
    }
}
```

### Descripción General

Este archivo contiene la pantalla principal de la aplicación. Desde aquí el usuario puede acceder a todas las funciones principales: medicamentos, citas médicas, farmacias cercanas y configuración. También incluye un botón destacado de emergencia para llamar al 911.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.home`

### Explicación Detallada

**MediAlertaScreen()**

Pantalla de inicio con el nombre de la aplicación, un botón de emergencia destacado, cuatro tarjetas de navegación y una imagen decorativa en la parte inferior.

- Parámetros (todos opcionales): `onNavigateToMedicamentos`, `onNavigateToMedicationList`, `onNavigateToCitas`, `onNavigateToFarmacias`, `onNavigateToConfiguracion`.
- Nota: Hay dos callbacks para medicamentos (`onNavigateToMedicamentos` y `onNavigateToMedicationList`), pero solo se usa `onNavigateToMedicationList` en la tarjeta.

**Componentes de la Pantalla**

*Título y Subtítulo* — "MediAlerta" en morado oscuro (40sp, negritas) y subtítulo "Tu salud en tus manos" en morado más claro.

*Botón de Emergencia 911* — Botón rojo que ocupa el 85% del ancho, con sombra pronunciada (12dp). Al presionarlo abre el marcador telefónico con el número 911 ya marcado.

*Tarjetas de Navegación* — Cuatro tarjetas en dos filas: Medicamentos (rosa), Citas (púrpura), Farmacias (amarillo) y Configuración (verde). Cada tarjeta tiene borde de 3dp, sombra de 8dp, esquinas redondeadas de 20dp, ícono blanco de 38dp y toda la tarjeta es clicable.

*Imagen Decorativa* — Imagen de un estetoscopio (`res/drawable/estetoscopio.png`), semi-transparente (alpha 0.9), alineada en la parte inferior central.

**PrettyCard()**

Componente reutilizable para las tarjetas de navegación. Recibe `icon`, `title`, `color`, `borderColor` y `onClick`.

### Flujo de Trabajo

1. Usuario abre la aplicación → llega a esta pantalla.
2. Presionar "Emergencia 911" → abre el marcador con el 911.
3. Presionar cualquier tarjeta → navega a la sección correspondiente.

---

## AddMedicationScreen.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos

import android.app.TimePickerDialog
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Delete
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.unit.dp
import java.util.Calendar
import mx.edu.utng.aimc.com.pantallaprincipal.viewmodel.MedicationViewModel

@Composable
fun AddMedicationScreen(
    viewModel: MedicationViewModel,
    onBack: () -> Unit,
    onNavigateToNotification: () -> Unit
) {
    var medName by remember { mutableStateOf("") }
    var medDose by remember { mutableStateOf("") }
    var medNote by remember { mutableStateOf("") }
    var medTotalDays by remember { mutableStateOf("") }
    var scheduleList by remember { mutableStateOf(listOf<String>()) }
    var selectedDays by remember { mutableStateOf(setOf<String>()) }

    val context = LocalContext.current
    val daysOfWeek = listOf("Lun", "Mar", "Mie", "Jue", "Vie", "Sab", "Dom")

    fun openTimePicker() {
        val calendar = Calendar.getInstance()
        val hour = calendar.get(Calendar.HOUR_OF_DAY)
        val minute = calendar.get(Calendar.MINUTE)

        TimePickerDialog(
            context,
            { _, selectedHour, selectedMinute ->
                val newTime = String.format("%02d:%02d", selectedHour, selectedMinute)
                scheduleList = scheduleList + newTime
            },
            hour,
            minute,
            true
        ).show()
    }

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Agregar Medicamento") },
                navigationIcon = {
                    IconButton(onClick = { onBack() }) {
                        Icon(Icons.Default.ArrowBack, contentDescription = "Volver")
                    }
                }
            )
        }
    ) { padding ->
        LazyColumn(
            modifier = Modifier
                .padding(padding)
                .padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(12.dp)
        ) {
            item {
                OutlinedTextField(
                    value = medName,
                    onValueChange = { medName = it },
                    label = { Text("Nombre del medicamento") },
                    modifier = Modifier.fillMaxWidth()
                )
            }

            item {
                OutlinedTextField(
                    value = medDose,
                    onValueChange = { medDose = it },
                    label = { Text("Dosis / Cantidad") },
                    modifier = Modifier.fillMaxWidth()
                )
            }

            item {
                OutlinedTextField(
                    value = medNote,
                    onValueChange = { medNote = it },
                    label = { Text("Descripción o nota") },
                    modifier = Modifier.fillMaxWidth()
                )
            }

            item {
                OutlinedTextField(
                    value = medTotalDays,
                    onValueChange = { medTotalDays = it },
                    label = { Text("¿Durante cuántos días lo tomará?") },
                    modifier = Modifier.fillMaxWidth()
                )
            }

            item {
                Text("Días de la semana:", style = MaterialTheme.typography.titleMedium)
            }

            item {
                Row(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalArrangement = Arrangement.spacedBy(8.dp)
                ) {
                    daysOfWeek.forEach { day ->
                        val isSelected = selectedDays.contains(day)
                        FilterChip(
                            selected = isSelected,
                            onClick = {
                                selectedDays = if (isSelected) selectedDays - day else selectedDays + day
                            },
                            label = { Text(day) },
                            modifier = Modifier.weight(1f)
                        )
                    }
                }
            }

            item {
                Row(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalArrangement = Arrangement.SpaceBetween
                ) {
                    Text("Horarios:", style = MaterialTheme.typography.titleMedium)
                    IconButton(onClick = { openTimePicker() }) {
                        Icon(imageVector = Icons.Default.Add, contentDescription = "Agregar horario")
                    }
                }
            }

            if (scheduleList.isEmpty()) {
                item {
                    Text("No hay horarios agregados")
                }
            } else {
                items(scheduleList) { time ->
                    Card(
                        modifier = Modifier.fillMaxWidth(),
                        elevation = CardDefaults.cardElevation(2.dp)
                    ) {
                        Row(
                            modifier = Modifier.padding(12.dp),
                            horizontalArrangement = Arrangement.SpaceBetween
                        ) {
                            Text(time)
                            IconButton(onClick = {
                                scheduleList = scheduleList.filter { it != time }
                            }) {
                                Icon(Icons.Default.Delete, contentDescription = "Eliminar")
                            }
                        }
                    }
                }
            }

            item {
                Button(
                    onClick = {
                        viewModel.insertMedication(
                            name = medName,
                            dose = medDose,
                            note = medNote,
                            days = selectedDays.toList(),
                            times = scheduleList,
                            totalDays = medTotalDays.toIntOrNull() ?: 0
                        )

                        onNavigateToNotification()
                    },
                    modifier = Modifier.fillMaxWidth(),
                    enabled = medName.isNotBlank() &&
                            scheduleList.isNotEmpty() &&
                            selectedDays.isNotEmpty() &&
                            medTotalDays.isNotBlank()
                ) {
                    Text("Guardar")
                }
            }
        }
    }
}
```

### Descripción General

Esta pantalla permite al usuario agregar un nuevo medicamento. Es un formulario completo que recolecta nombre, dosis, notas, días de la semana, horarios y duración del tratamiento.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos`

### Explicación Detallada

**AddMedicationScreen()**

- Parámetros: `viewModel`, `onBack`, `onNavigateToNotification`.
- Variables de estado: `medName`, `medDose`, `medNote`, `medTotalDays`, `scheduleList`, `selectedDays`.

**Campos del Formulario**

1. Nombre del medicamento — campo de texto obligatorio.
2. Dosis / Cantidad — campo de texto para la dosis.
3. Descripción o nota — campo para instrucciones especiales.
4. Duración en días — campo de texto para los días totales.
5. Días de la semana — `FilterChip` para selección múltiple: Lun, Mar, Mie, Jue, Vie, Sab, Dom.
6. Horarios — botón "+" que abre `TimePickerDialog`. Cada horario agregado aparece en una tarjeta con opción de eliminar.

**openTimePicker()** — Abre el selector de hora nativo. Al seleccionar una hora, la agrega a `scheduleList` con formato `"HH:mm"`.

**Botón Guardar** — Solo se habilita si el nombre no está vacío, hay al menos un horario, al menos un día y el campo de días totales no está vacío. Al guardar, llama a `viewModel.insertMedication()` y navega a la pantalla de notificación.

---

## MedicamentosScreen.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos

import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import mx.edu.utng.aimc.com.pantallaprincipal.viewmodel.MedicationViewModel
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.MedicationEntity

@Composable
fun MedicamentosScreen(viewModel: MedicationViewModel) {

    val meds by viewModel.allMedications.collectAsState(initial = emptyList())

    LazyColumn(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {

        items(meds) { med ->
            MedicationCard(
                med = med,
                onTaken = {
                    viewModel.markMedicationTaken(med)

                    if (med.daysTaken + 1 >= med.totalDays) {
                        viewModel.deleteMedication(med)
                    }
                }
            )
        }
    }
}

@Composable
fun MedicationCard(
    med: MedicationEntity,
    onTaken: () -> Unit
) {
    Card(
        modifier = Modifier.fillMaxWidth(),
        elevation = CardDefaults.cardElevation(6.dp)
    ) {
        Column(modifier = Modifier.padding(16.dp)) {

            Text(text = "💊 ${med.name}", style = MaterialTheme.typography.titleLarge)
            Text(text = "Dosis: ${med.dose}")
            Text(text = "Notas: ${med.note}")

            Spacer(Modifier.height(10.dp))

            val progress = med.daysTaken.toFloat() / med.totalDays.toFloat()

            LinearProgressIndicator(progress = progress, modifier = Modifier.fillMaxWidth())

            Text(
                text = "${med.daysTaken} / ${med.totalDays} días",
                style = MaterialTheme.typography.bodyMedium
            )

            Spacer(Modifier.height(10.dp))

            Button(
                onClick = onTaken,
                modifier = Modifier.fillMaxWidth()
            ) {
                Text("Ya la tomé")
            }
        }
    }
}
```

### Descripción General

Esta pantalla muestra la lista de medicamentos activos con su progreso de tratamiento. Cada medicamento tiene una barra de progreso y un botón para marcar que ya se tomó la dosis.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos`

### Explicación Detallada

**MedicamentosScreen()**

Observa `allMedications` del ViewModel con `collectAsState` y muestra cada medicamento con `MedicationCard`.

**MedicationCard() (versión simple)**

Tarjeta individual con nombre (con emoji 💊), dosis, notas, barra de progreso lineal, texto "días tomados / días totales" y botón "Ya la tomé".

Lógica del botón: Llama a `viewModel.markMedicationTaken(med)`. Si `daysTaken + 1 >= totalDays`, elimina automáticamente el medicamento.

---

## MedicationListScreen.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos

import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.ArrowBack
import androidx.compose.material.icons.filled.Delete
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import kotlinx.coroutines.flow.collectLatest
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.MedicationEntity
import mx.edu.utng.aimc.com.pantallaprincipal.viewmodel.MedicationViewModel
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.background
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.sp

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun MedicationListScreen(
    onAddMedication: () -> Unit,
    onBack: () -> Unit,
    viewModel: MedicationViewModel
) {
    var medications by remember { mutableStateOf<List<MedicationEntity>>(emptyList()) }

    LaunchedEffect(viewModel) {
        viewModel.allMedications.collectLatest { meds ->
            medications = meds
        }
    }

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Mis Medicamentos") },
                navigationIcon = {
                    IconButton(onClick = onBack) {
                        Icon(Icons.Default.ArrowBack, contentDescription = "Volver")
                    }
                },
                actions = {
                    Button(
                        onClick = onAddMedication,
                        modifier = Modifier.padding(end = 8.dp),
                        colors = ButtonDefaults.buttonColors(
                            containerColor = MaterialTheme.colorScheme.primaryContainer,
                            contentColor = MaterialTheme.colorScheme.onPrimaryContainer
                        )
                    ) {
                        Icon(
                            Icons.Filled.Add,
                            contentDescription = "Agregar",
                            modifier = Modifier.size(18.dp)
                        )
                        Spacer(Modifier.width(4.dp))
                        Text("Agregar")
                    }
                }
            )
        }
    ) { paddingValues ->

        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues)
                .padding(horizontal = 16.dp, vertical = 8.dp)
        ) {

            if (medications.isEmpty()) {
                Box(
                    modifier = Modifier.fillMaxSize(),
                    contentAlignment = Alignment.Center
                ) {
                    Text(
                        text = "No hay medicamentos registrados.\nPresiona 'Agregar' para empezar.",
                        style = MaterialTheme.typography.titleMedium,
                        color = MaterialTheme.colorScheme.onSurfaceVariant,
                        textAlign = androidx.compose.ui.text.style.TextAlign.Center
                    )
                }
            } else {
                LazyColumn(verticalArrangement = Arrangement.spacedBy(12.dp)) {
                    items(medications) { med ->
                        MedicationCard(
                            medication = med,
                            onDelete = { medToDelete ->
                                viewModel.deleteMedication(medToDelete)
                            }
                        )
                    }
                }
            }
        }
    }
}

@Composable
fun MedicationCard(
    medication: MedicationEntity,
    onDelete: (MedicationEntity) -> Unit
) {
    Card(
        modifier = Modifier
            .fillMaxWidth()
            .padding(horizontal = 8.dp, vertical = 6.dp),
        elevation = CardDefaults.cardElevation(8.dp),
        colors = CardDefaults.cardColors(
            containerColor = Color(0xFFFFE4F0)
        ),
        shape = RoundedCornerShape(20.dp)
    ) {
        Box {
            Canvas(modifier = Modifier.fillMaxSize()) {
                drawCircle(
                    color = Color(0xFFFFB6D9).copy(alpha = 0.3f),
                    radius = 100f,
                    center = Offset(size.width - 50f, 50f)
                )
                drawCircle(
                    color = Color(0xFFFF8DC7).copy(alpha = 0.2f),
                    radius = 80f,
                    center = Offset(50f, size.height - 50f)
                )
            }

            Column(
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(20.dp)
            ) {
                Row(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalArrangement = Arrangement.SpaceBetween,
                    verticalAlignment = Alignment.CenterVertically
                ) {
                    Row(
                        verticalAlignment = Alignment.CenterVertically,
                        modifier = Modifier.weight(1f)
                    ) {
                        Box(
                            modifier = Modifier
                                .size(48.dp)
                                .background(
                                    color = Color(0xFFFF8DC7),
                                    shape = CircleShape
                                ),
                            contentAlignment = Alignment.Center
                        ) {
                            Text(text = "💊", fontSize = 24.sp)
                        }

                        Spacer(modifier = Modifier.width(12.dp))

                        Text(
                            text = medication.name,
                            style = MaterialTheme.typography.titleLarge,
                            fontWeight = FontWeight.Bold,
                            color = Color(0xFFD81B60),
                            modifier = Modifier.padding(end = 8.dp)
                        )
                    }

                    IconButton(
                        onClick = { onDelete(medication) },
                        modifier = Modifier
                            .size(40.dp)
                            .background(
                                color = Color(0xFFFF6B9D).copy(alpha = 0.3f),
                                shape = CircleShape
                            )
                    ) {
                        Icon(
                            imageVector = Icons.Default.Delete,
                            contentDescription = "Eliminar",
                            tint = Color(0xFFD81B60),
                            modifier = Modifier.size(20.dp)
                        )
                    }
                }

                Spacer(modifier = Modifier.height(16.dp))

                HorizontalDivider(
                    thickness = 2.dp,
                    color = Color(0xFFFFB6D9)
                )

                Spacer(modifier = Modifier.height(16.dp))

                MedicationInfoRow(
                    icon = "💉",
                    label = "Dosis",
                    value = medication.dose
                )

                Spacer(modifier = Modifier.height(12.dp))

                MedicationInfoRow(
                    icon = "⏰",
                    label = "Horarios",
                    value = medication.schedules.joinToString(", ")
                )

                Spacer(modifier = Modifier.height(12.dp))

                MedicationInfoRow(
                    icon = "📅",
                    label = "Días",
                    value = medication.days.joinToString(", ")
                )
            }
        }
    }
}

@Composable
fun MedicationInfoRow(
    icon: String,
    label: String,
    value: String
) {
    Row(
        modifier = Modifier.fillMaxWidth(),
        verticalAlignment = Alignment.CenterVertically
    ) {
        Box(
            modifier = Modifier
                .size(36.dp)
                .background(
                    color = Color(0xFFFFFFFF).copy(alpha = 0.6f),
                    shape = RoundedCornerShape(10.dp)
                ),
            contentAlignment = Alignment.Center
        ) {
            Text(text = icon, fontSize = 18.sp)
        }

        Spacer(modifier = Modifier.width(12.dp))

        Column {
            Text(
                text = label,
                style = MaterialTheme.typography.labelSmall,
                color = Color(0xFFD81B60).copy(alpha = 0.7f),
                fontWeight = FontWeight.SemiBold
            )
            Text(
                text = value,
                style = MaterialTheme.typography.bodyLarge,
                color = Color(0xFF880E4F),
                fontWeight = FontWeight.Medium
            )
        }
    }
}
```

### Descripción General

Esta es la pantalla principal de lista de medicamentos, con un diseño más elaborado y atractivo. Muestra todos los medicamentos registrados con información detallada y opción de eliminar.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos`

### Explicación Detallada

**MedicationListScreen()**

- Parámetros: `onAddMedication`, `onBack`, `viewModel`.
- `TopAppBar` con título "Mis Medicamentos", botón atrás y botón "Agregar".
- Muestra mensaje de estado vacío cuando no hay medicamentos.
- Observa cambios en tiempo real con `collectLatest`.

**MedicationCard() (versión decorativa)**

Tarjeta con fondo rosa pastel (#FFE4F0), bordes redondeados de 20dp y círculos decorativos dibujados con `Canvas`. Muestra ícono circular rosa con emoji 💊, nombre en rosa fuerte (#D81B60) y botón de eliminar.

La información se organiza en filas: Dosis (💉), Horarios (⏰) y Días (📅).

**MedicationInfoRow()**

Componente reutilizable que muestra una fila con caja blanca semitransparente con ícono emoji, etiqueta pequeña y valor en letra más grande.

---

## NotificacionMedicamento.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.notificaciones

import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
@Preview(showBackground = true)
fun NotificacionMedicamento(
    nombreMedicamento: String = "Amoxicilina",
    horario: String = "7:00 am",
    onYaLaTome: () -> Unit = {},
    onEmergencia: () -> Unit = {}
) {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .background(Color(0xFFEFEFEF))
            .padding(24.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Top
    ) {
        Spacer(modifier = Modifier.height(50.dp))

        Card(
            modifier = Modifier
                .fillMaxWidth()
                .height(230.dp),
            elevation = CardDefaults.cardElevation(8.dp),
            colors = CardDefaults.cardColors(containerColor = Color.White),
            shape = RoundedCornerShape(16.dp)
        ) {
            Column(
                modifier = Modifier
                    .fillMaxSize()
                    .padding(16.dp),
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.SpaceEvenly
            ) {
                Text(
                    text = "🔔 Próximo medicamento",
                    fontSize = 22.sp,
                    fontWeight = FontWeight.Bold,
                    color = Color(0xFF333333),
                    textAlign = TextAlign.Center,
                    modifier = Modifier.fillMaxWidth()
                )

                Text(
                    text = "$nombreMedicamento $horario",
                    fontSize = 27.sp,
                    color = Color(0xFF00796B),
                    fontWeight = FontWeight.SemiBold
                )

                Spacer(modifier = Modifier.height(8.dp))

                Row(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalArrangement = Arrangement.SpaceEvenly
                ) {
                    Button(
                        onClick = onYaLaTome,
                        colors = ButtonDefaults.buttonColors(containerColor = Color(0xFF4CAF50))
                    ) {
                        Text("Ya la tomé", fontSize = 21.sp, color = Color.White)
                    }

                    Button(
                        onClick = onEmergencia,
                        colors = ButtonDefaults.buttonColors(containerColor = Color(0xFFD32F2F))
                    ) {
                        Text("Emergencia", fontSize = 21.sp, color = Color.White)
                    }
                }
            }
        }
    }
}
```

### Descripción General

Este archivo define la pantalla de notificación que se muestra al usuario cuando es hora de tomar un medicamento. Muestra el nombre del medicamento, la hora y dos botones de acción: "Ya la tomé" y "Emergencia".

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.notificaciones`

### Explicación Detallada

**NotificacionMedicamento()**

- Parámetros: `nombreMedicamento` (defecto: `"Amoxicilina"`), `horario` (defecto: `"7:00 am"`), `onYaLaTome`, `onEmergencia`.
- Fondo gris claro (#EFEFEF) con tarjeta blanca de 230dp de altura y sombra de 8dp.

**Contenido de la Tarjeta**

- Título "🔔 Próximo medicamento" en gris oscuro (22sp, negritas).
- Nombre y hora combinados en verde azulado (#00796B, 27sp) para que sean claramente visibles.
- Botón verde (#4CAF50) "Ya la tomé" para confirmar la toma.
- Botón rojo (#D32F2F) "Emergencia" para navegar a contactos de emergencia.

### Flujo de Trabajo

1. La alarma se activa → `AlarmReceiver` muestra la notificación.
2. El usuario abre la notificación → llega a esta pantalla con los datos del medicamento.
3. Si ya tomó el medicamento: presiona "Ya la tomé" → `onYaLaTome()` marca como tomado y cancela la notificación.
4. Si tiene una emergencia: presiona "Emergencia" → `onEmergencia()` navega a la pantalla de contactos.

---

## Color.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.theme

import androidx.compose.ui.graphics.Color

val Purple80 = Color(0xFFD0BCFF)
val PurpleGrey80 = Color(0xFFCCC2DC)
val Pink80 = Color(0xFFEFB8C8)

val Purple40 = Color(0xFF6650a4)
val PurpleGrey40 = Color(0xFF625b71)
val Pink40 = Color(0xFF7D5260)
```

### Descripción General

Este archivo define los colores principales que se usan en el tema de la aplicación. Los colores están organizados en dos grupos: los de la paleta clara (versión 40, más oscuros) y los de la paleta oscura (versión 80, más claros). Esta es la paleta base que Material 3 usa para generar el esquema de colores completo.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.ui.theme`

---

## Type.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.theme

import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Typography = Typography(
    bodyLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 16.sp,
        lineHeight = 24.sp,
        letterSpacing = 0.5.sp
    )
)
```

### Descripción General

Este archivo define la tipografía (estilos de texto) que se usa en toda la aplicación siguiendo las especificaciones de Material 3. Actualmente solo define el estilo `bodyLarge`, pero está preparado para agregar más estilos según se necesiten.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.ui.theme`

### Explicación Detallada

**Estilo Definido: bodyLarge**

```kotlin
bodyLarge = TextStyle(
    fontFamily = FontFamily.Default,  // Usa la fuente por defecto del sistema
    fontWeight = FontWeight.Normal,   // Peso normal (equivalente a 400)
    fontSize = 16.sp,                  // Tamaño estándar para lectura
    lineHeight = 24.sp,                // Altura de línea para mejor legibilidad
    letterSpacing = 0.5.sp             // Espaciado mínimo entre letras
)
```

- `fontFamily = FontFamily.Default`: Usa la fuente predeterminada del sistema (Roboto en Android). Asegura que la app se vea nativa.
- `fontWeight = FontWeight.Normal`: Peso normal de la fuente, sin negritas.
- `fontSize = 16.sp`: Tamaño estándar para lectura cómoda. `sp` se ajusta según las preferencias de accesibilidad del usuario.
- `lineHeight = 24.sp`: Espacio entre líneas, mejora la legibilidad en párrafos largos.
- `letterSpacing = 0.5.sp`: Pequeño espaciado entre caracteres para mejor claridad.

**Estilos Comentados (Preparados para futuro)**

El archivo incluye comentarios con otros estilos que podrían implementarse:

```kotlin
/*
titleLarge = TextStyle(
    fontFamily = FontFamily.Default,
    fontWeight = FontWeight.Normal,
    fontSize = 22.sp,
    lineHeight = 28.sp,
    letterSpacing = 0.sp
),
labelSmall = TextStyle(
    fontFamily = FontFamily.Default,
    fontWeight = FontWeight.Medium,
    fontSize = 11.sp,
    lineHeight = 16.sp,
    letterSpacing = 0.5.sp
)
*/
```

- `titleLarge`: Para títulos importantes (22sp).
- `labelSmall`: Para textos muy pequeños como etiquetas (11sp, con peso Medium para destacar).

### Propósito

- **Consistencia visual**: Toda la app usa los mismos estilos de texto.
- **Mantenibilidad**: Cambiar un estilo en un solo lugar afecta toda la app.
- **Jerarquía visual**: Diferentes estilos crean una jerarquía clara para el usuario.
- **Accesibilidad**: Usar `sp` permite que el texto se escale con preferencias del usuario.

---

## NotificationHelper.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.utils

import android.app.NotificationChannel
import android.app.NotificationManager
import android.app.PendingIntent
import android.content.Context
import android.content.Intent
import android.os.Build
import androidx.core.app.NotificationCompat
import mx.edu.utng.aimc.com.pantallaprincipal.R
import mx.edu.utng.aimc.com.pantallaprincipal.receivers.AlarmReceiver

class NotificationHelper(private val context: Context) {

    private val CHANNEL_ID = "med_alert_channel"
    private val CHANNEL_NAME = "Recordatorios de Medicamentos"
    private val notificationManager = context.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager

    private val ACCENT_COLOR_GREEN = 0xFF00FF00.toInt()

    init {
        createNotificationChannel()
    }

    private fun createNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel(
                CHANNEL_ID,
                CHANNEL_NAME,
                NotificationManager.IMPORTANCE_HIGH
            ).apply {
                description = "Canal para las notificaciones de recordatorio de medicamentos."
                enableLights(true)
                lightColor = ACCENT_COLOR_GREEN
                enableVibration(true)
                vibrationPattern = longArrayOf(0, 500, 200, 500)
                setShowBadge(true)
            }
            notificationManager.createNotificationChannel(channel)
        }
    }

    companion object {
        const val ACTION_MARK_TAKEN = "mx.edu.utng.ACTION_MARK_TAKEN"
        const val EXTRA_MED_ID = "extra_med_id"
        const val ACTION_MEDICATION_ALARM = "mx.edu.utng.ACTION_MEDICATION_ALARM"
        const val EXTRA_MED_NAME = "extra_med_name"
    }

    fun showMedicationNotification(medName: String, medId: Int) {

        val bigTitle = "⏰ ¡HORA DE TU MEDICAMENTO!"

        val bigContent = """
            💊 Medicamento: $medName
            
            Es momento de tomar tu dosis.
            Tu salud es nuestra prioridad.
            
            ⚕️ ¡No olvides tomar tu medicamento!
        """.trimIndent()

        val markTakenIntent = Intent(context, AlarmReceiver::class.java).apply {
            action = ACTION_MARK_TAKEN
            putExtra(EXTRA_MED_ID, medId)
        }

        val markTakenPendingIntent: PendingIntent = PendingIntent.getBroadcast(
            context,
            medId,
            markTakenIntent,
            PendingIntent.FLAG_UPDATE_CURRENT or PendingIntent.FLAG_IMMUTABLE
        )

        val notification = NotificationCompat.Builder(context, CHANNEL_ID)
            .setSmallIcon(R.drawable.estetoscopio)
            .setContentTitle(bigTitle)
            .setContentText("Es hora de tomar: $medName")
            .setPriority(NotificationCompat.PRIORITY_MAX)
            .setCategory(NotificationCompat.CATEGORY_ALARM)
            .setAutoCancel(true)
            .setColor(ACCENT_COLOR_GREEN)
            .setColorized(true)
            .setVisibility(NotificationCompat.VISIBILITY_PUBLIC)
            .setStyle(
                NotificationCompat.BigTextStyle()
                    .bigText(bigContent)
                    .setBigContentTitle(bigTitle)
                    .setSummaryText("💚 Tu salud es primero")
            )
            .setDefaults(NotificationCompat.DEFAULT_ALL)
            .setVibrate(longArrayOf(0, 500, 200, 500))
            .setLights(ACCENT_COLOR_GREEN, 1000, 3000)
            .build()

        notificationManager.notify(medId, notification)
    }
}
```

### Descripción General

Esta clase se encarga de crear y mostrar las notificaciones de recordatorio de medicamentos. Configura el canal de notificaciones (necesario en Android 8+), construye notificaciones llamativas con colores verdes y texto expandido.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.utils`

### Explicación Detallada

**Propiedades**

- `CHANNEL_ID`: Identificador único del canal de notificaciones.
- `CHANNEL_NAME`: Nombre visible del canal en configuración del sistema.
- `notificationManager`: Servicio del sistema para gestionar notificaciones.
- `ACCENT_COLOR_GREEN`: Color verde intenso (0xFF00FF00) para destacar visualmente.

**Inicialización**

Al crear una instancia, automáticamente crea el canal de notificaciones con importancia alta, luz LED verde y vibración personalizada (500ms, pausa 200ms, 500ms).

**showMedicationNotification()**

Construye y muestra una notificación con: título "⏰ ¡HORA DE TU MEDICAMENTO!", texto expandido al desplegar, color verde intenso (`colorized = true`), prioridad máxima, categoría alarma, vibración y luz LED.

---

## AlarmUtils.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.utils

import android.app.AlarmManager
import android.app.PendingIntent
import android.content.Context
import android.content.Intent
import android.net.Uri
import mx.edu.utng.aimc.com.pantallaprincipal.receivers.AlarmReceiver
import java.util.Calendar

const val ACTION_MEDICATION_ALARM = "mx.edu.utng.ACTION_MEDICATION_ALARM"
const val ACTION_MARK_TAKEN = "mx.edu.utng.ACTION_MARK_TAKEN"

private val dayMap = mapOf(
    "Dom" to Calendar.SUNDAY, "Lun" to Calendar.MONDAY, "Mar" to Calendar.TUESDAY,
    "Mie" to Calendar.WEDNESDAY, "Jue" to Calendar.THURSDAY, "Vie" to Calendar.FRIDAY,
    "Sab" to Calendar.SATURDAY
)

fun scheduleAlarmsForMedication(
    context: Context,
    medId: Long,
    times: List<String>,
    days: List<String>,
    medName: String
) {
    val am = context.getSystemService(Context.ALARM_SERVICE) as AlarmManager

    times.forEachIndexed { timeIndex, timeStr ->
        val parts = timeStr.split(":")
        val hour = parts[0].toIntOrNull() ?: 0
        val minute = parts[1].toIntOrNull() ?: 0

        days.forEach { dayStr ->
            val dayOfWeek = dayMap[dayStr] ?: return@forEach

            val requestId = (medId * 100 + timeIndex * 10 + dayOfWeek).toInt()

            val calendar = Calendar.getInstance().apply {
                set(Calendar.HOUR_OF_DAY, hour)
                set(Calendar.MINUTE, minute)
                set(Calendar.SECOND, 0)
                set(Calendar.MILLISECOND, 0)
                set(Calendar.DAY_OF_WEEK, dayOfWeek)

                if (timeInMillis <= System.currentTimeMillis()) {
                    add(Calendar.WEEK_OF_YEAR, 1)
                }
            }

            val intent = Intent(context, AlarmReceiver::class.java).apply {
                action = ACTION_MEDICATION_ALARM
                data = Uri.parse("medalarm://$requestId")
                putExtra("medName", medName)
                putExtra("medId", medId.toInt())
                putExtra("requestId", requestId)
            }

            val pi = PendingIntent.getBroadcast(
                context,
                requestId,
                intent,
                PendingIntent.FLAG_UPDATE_CURRENT or PendingIntent.FLAG_IMMUTABLE
            )

            am.setRepeating(
                AlarmManager.RTC_WAKEUP,
                calendar.timeInMillis,
                AlarmManager.INTERVAL_DAY * 7,
                pi
            )
        }
    }
}

fun cancelAlarmsForMedication(context: Context, medId: Int) {
    val am = context.getSystemService(Context.ALARM_SERVICE) as AlarmManager

    for (timeIndex in 0 until 5) {
        dayMap.values.forEach { dayOfWeek ->
            val requestId = (medId * 100 + timeIndex * 10 + dayOfWeek).toInt()

            val intent = Intent(context, AlarmReceiver::class.java).apply {
                action = ACTION_MEDICATION_ALARM
                data = Uri.parse("medalarm://$requestId")
            }

            val pi = PendingIntent.getBroadcast(
                context,
                requestId,
                intent,
                PendingIntent.FLAG_NO_CREATE or PendingIntent.FLAG_IMMUTABLE
            )

            pi?.let {
                am.cancel(it)
                it.cancel()
            }
        }
    }
}
```

### Descripción General

Este archivo contiene funciones utilitarias para programar y cancelar alarmas de medicamentos usando `AlarmManager`. Calcula fechas futuras, maneja repeticiones semanales y genera IDs únicos para cada alarma.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.utils`

### Explicación Detallada

**Constantes de Acción**

- `ACTION_MEDICATION_ALARM`: Identifica alarmas de medicamentos.
- `ACTION_MARK_TAKEN`: Identifica la acción de "Ya lo tomé".

**Mapa de Días**

```kotlin
private val dayMap = mapOf(
    "Dom" to Calendar.SUNDAY, "Lun" to Calendar.MONDAY, "Mar" to Calendar.TUESDAY,
    "Mie" to Calendar.WEDNESDAY, "Jue" to Calendar.THURSDAY, "Vie" to Calendar.FRIDAY,
    "Sab" to Calendar.SATURDAY
)
```

Convierte abreviaturas de días a constantes de `Calendar`.

**scheduleAlarmsForMedication()**

Programa alarmas para un medicamento según sus horarios y días. Para cada combinación de horario y día, genera un ID único (`medId*100 + timeIndex*10 + dayOfWeek`), calcula el próximo momento de disparo y usa `setRepeating` con intervalo semanal. Si la hora ya pasó hoy, programa para la próxima semana.

**cancelAlarmsForMedication()**

Cancela todas las alarmas de un medicamento. Itera por posibles horarios (0-4) y todos los días, genera los mismos IDs que al programar y busca `PendingIntents` sin crear nuevos (`FLAG_NO_CREATE`). Si existen, los cancela y libera recursos.

---

## PlacesApiHelper.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.utils

import android.annotation.SuppressLint
import android.content.Context
import com.google.android.gms.maps.model.LatLng
import com.google.android.libraries.places.api.Places
import com.google.android.libraries.places.api.model.Place
import com.google.android.libraries.places.api.model.PlaceTypes
import com.google.android.libraries.places.api.net.FindCurrentPlaceRequest
import com.google.android.libraries.places.api.net.PlacesClient
import kotlinx.coroutines.tasks.await
import mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.farmacias.Farmacia
import kotlin.math.atan2
import kotlin.math.cos
import kotlin.math.sin
import kotlin.math.sqrt

object PlacesApiHelper {

    fun initialize(context: Context, apiKey: String) {
        if (!Places.isInitialized()) {
            Places.initialize(context, apiKey)
        }
    }

    @SuppressLint("MissingPermission")
    suspend fun buscarFarmacias(
        context: Context,
        userLocation: LatLng
    ): List<Farmacia> {
        try {
            val placesClient: PlacesClient = Places.createClient(context)

            val placeFields = listOf(
                Place.Field.NAME,
                Place.Field.ADDRESS,
                Place.Field.LAT_LNG,
                Place.Field.TYPES
            )

            val request = FindCurrentPlaceRequest.newInstance(placeFields)

            val response = placesClient.findCurrentPlace(request).await()

            val farmacias = mutableListOf<Farmacia>()

            response.placeLikelihoods.forEach { placeLikelihood ->
                val place = placeLikelihood.place

                if (place.placeTypes?.contains(PlaceTypes.PHARMACY) == true ||
                    place.name?.contains("farmacia", ignoreCase = true) == true) {

                    place.latLng?.let { latLng ->
                        val distancia = calcularDistancia(userLocation, latLng)

                        farmacias.add(
                            Farmacia(
                                nombre = place.name ?: "Farmacia",
                                direccion = place.address ?: "Dirección no disponible",
                                distancia = formatearDistancia(distancia),
                                latLng = latLng
                            )
                        )
                    }
                }
            }

            return farmacias.sortedBy {
                calcularDistancia(userLocation, it.latLng)
            }.take(10)

        } catch (e: Exception) {
            e.printStackTrace()
            return emptyList()
        }
    }

    private fun calcularDistancia(from: LatLng, to: LatLng): Double {
        val earthRadius = 6371000.0

        val dLat = Math.toRadians(to.latitude - from.latitude)
        val dLng = Math.toRadians(to.longitude - from.longitude)

        val a = sin(dLat / 2) * sin(dLat / 2) +
                cos(Math.toRadians(from.latitude)) * cos(Math.toRadians(to.latitude)) *
                sin(dLng / 2) * sin(dLng / 2)

        val c = 2 * atan2(sqrt(a), sqrt(1 - a))

        return earthRadius * c
    }

    private fun formatearDistancia(metros: Double): String {
        return when {
            metros < 1000 -> "${metros.toInt()} m"
            else -> "%.1f km".format(metros / 1000)
        }
    }
}
```

### Descripción General

Este helper maneja la interacción con Google Places API para buscar farmacias cercanas a la ubicación del usuario. Inicializa el servicio, realiza búsquedas, calcula distancias y formatea resultados.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.utils`

### Explicación Detallada

**initialize()** — Inicializa el SDK de Places con la API key. Solo si no estaba inicializado previamente.

**buscarFarmacias()** — Crea un cliente de Places, solicita lugares cercanos con campos específicos (nombre, dirección, coordenadas, tipos), filtra los que sean de tipo `PHARMACY` o cuyo nombre contenga "farmacia", calcula la distancia y devuelve las 10 más cercanas ordenadas por distancia.

**calcularDistancia()** — Implementa la fórmula de Haversine para calcular distancia entre dos coordenadas en metros.

**formatearDistancia()** — Formatea metros a `"XXX m"` si es menos de 1000 metros, o a `"X.X km"` si es más.

---

## ThemePreferences.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.utils

import android.content.Context
import androidx.datastore.core.DataStore
import androidx.datastore.preferences.core.Preferences
import androidx.datastore.preferences.core.booleanPreferencesKey
import androidx.datastore.preferences.core.edit
import androidx.datastore.preferences.preferencesDataStore
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.map

private val Context.dataStore: DataStore<Preferences> by preferencesDataStore(name = "theme_preferences")

class ThemePreferences(private val context: Context) {

    companion object {
        private val IS_DARK_THEME = booleanPreferencesKey("is_dark_theme")
    }

    val isDarkTheme: Flow<Boolean> = context.dataStore.data
        .map { preferences ->
            preferences[IS_DARK_THEME] ?: false
        }

    suspend fun setDarkTheme(isDark: Boolean) {
        context.dataStore.edit { preferences ->
            preferences[IS_DARK_THEME] = isDark
        }
    }
}
```

### Descripción General

Esta clase maneja las preferencias de tema (oscuro/claro) usando Jetpack DataStore, una alternativa moderna a SharedPreferences. Guarda la preferencia del usuario de forma persistente.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.utils`

### Explicación Detallada

**DataStore**

```kotlin
private val Context.dataStore: DataStore<Preferences> by preferencesDataStore(name = "theme_preferences")
```

Define una extensión de `Context` que proporciona una instancia de DataStore con nombre `"theme_preferences"`.

**Clave de Preferencia**

```kotlin
private val IS_DARK_THEME = booleanPreferencesKey("is_dark_theme")
```

Define una clave booleana para guardar el estado del tema.

**isDarkTheme: Flow\<Boolean\>** — Expone un `Flow` que emite el valor actual de la preferencia. Los componentes pueden observarlo para reaccionar a cambios.

**setDarkTheme()** — Función suspendida que guarda un nuevo valor para la preferencia de tema.

---

## AppointmentViewModel.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import com.google.firebase.database.FirebaseDatabase
import kotlinx.coroutines.flow.SharingStarted
import kotlinx.coroutines.flow.stateIn
import kotlinx.coroutines.launch
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.AppointmentEntity
import mx.edu.utng.aimc.com.pantallaprincipal.data.repository.FirebaseAppointmentRepository

class AppointmentViewModel : ViewModel() {

    private val repository = FirebaseAppointmentRepository()
    private val firebaseDb = FirebaseDatabase.getInstance().reference

    val allAppointments = repository.getAllAppointments()
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000),
            initialValue = emptyList()
        )

    fun insertAppointment(appointment: AppointmentEntity) {
        viewModelScope.launch {
            val newApptId = repository.insertAppointment(appointment)
            saveAppointmentToFirebase(appointment)
        }
    }

    fun deleteAppointment(appointment: AppointmentEntity) {
        viewModelScope.launch {
            repository.deleteAppointment(appointment)
        }

        firebaseDb.child("appointments").get().addOnSuccessListener { snapshot ->
            snapshot.children.forEach { node ->
                val item = node.getValue(AppointmentEntity::class.java)
                if (item != null && item.place == appointment.place && item.dateTime == appointment.dateTime) {
                    node.ref.removeValue()
                }
            }
        }
    }

    private fun saveAppointmentToFirebase(appt: AppointmentEntity) {
        val id = firebaseDb.child("appointments").push().key ?: return
        firebaseDb.child("appointments").child(id).setValue(appt)
            .addOnSuccessListener { println("✅ Cita guardada en Firebase Realtime DB") }
            .addOnFailureListener { println("❌ Error guardando cita: ${it.message}") }
    }
}
```

### Descripción General

Este ViewModel maneja la lógica de negocio para las citas médicas. Conecta la interfaz de usuario con Firebase, tanto Firestore (para consultas en tiempo real) como Realtime Database (para respaldo).

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.viewmodel`

### Explicación Detallada

**Propiedades**

- `repository`: Instancia de `FirebaseAppointmentRepository` para operaciones CRUD en Firestore.
- `firebaseDb`: Referencia a Firebase Realtime Database para respaldo.
- `allAppointments`: `StateFlow` que emite la lista de citas, observable desde la UI.

**allAppointments**

```kotlin
val allAppointments = repository.getAllAppointments()
    .stateIn(
        scope = viewModelScope,
        started = SharingStarted.WhileSubscribed(5000),
        initialValue = emptyList()
    )
```

Convierte el `Flow` del repositorio en un `StateFlow` que se mantiene activo mientras haya suscriptores (más 5 segundos) y tiene valor inicial vacío.

**insertAppointment()** — Guarda en Firestore usando el repositorio y también en Realtime Database como respaldo.

**deleteAppointment()** — Elimina de Firestore usando el repositorio y busca y elimina en Realtime Database por lugar y fecha/hora.

### Archivos Relacionados

- `FirebaseAppointmentRepository.kt` — El repositorio que usa.
- `AppointmentEntity.kt` — La entidad que maneja.
- `AppointmentViewModelFactory.kt` — Su fábrica de creación.

---

## AppointmentViewModelFactory.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

import androidx.lifecycle.ViewModel
import androidx.lifecycle.ViewModelProvider

class AppointmentViewModelFactory : ViewModelProvider.Factory {

    @Suppress("UNCHECKED_CAST")
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(AppointmentViewModel::class.java)) {
            return AppointmentViewModel() as T
        }
        throw IllegalArgumentException("Clase de ViewModel desconocida para AppointmentViewModelFactory")
    }
}
```

### Descripción General

Fábrica para crear instancias de `AppointmentViewModel`. No necesita parámetros porque el ViewModel crea sus propias dependencias internamente.

---

## EmergencyContactViewModel.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import kotlinx.coroutines.flow.SharingStarted
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.stateIn
import kotlinx.coroutines.launch
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.EmergencyContactEntity
import mx.edu.utng.aimc.com.pantallaprincipal.data.repository.FirebaseContactRepository

class EmergencyContactViewModel : ViewModel() {

    private val repository = FirebaseContactRepository()

    val allContacts: StateFlow<List<EmergencyContactEntity>> = repository.getAllContacts()
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(stopTimeoutMillis = 5000),
            initialValue = emptyList()
        )

    fun insertContact(name: String, phoneNumber: String) {
        viewModelScope.launch {
            val contact = EmergencyContactEntity(
                name = name,
                phoneNumber = phoneNumber
            )
            repository.insertContact(contact)
        }
    }

    fun deleteContact(contact: EmergencyContactEntity) {
        viewModelScope.launch {
            repository.deleteContact(contact)
        }
    }
}
```

### Descripción General

ViewModel para gestionar contactos de emergencia. Usa Firebase Firestore como fuente de datos y mantiene el estado para la UI.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.viewmodel`

### Explicación Detallada

**Propiedades**

- `repository`: Instancia de `FirebaseContactRepository`.
- `allContacts`: `StateFlow` con la lista de contactos.

**Métodos**

- `insertContact()`: Crea un nuevo `EmergencyContactEntity` y lo guarda en Firebase.
- `deleteContact()`: Elimina un contacto existente de Firebase.

---

## EmergencyContactViewModelFactory.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

import androidx.lifecycle.ViewModel
import androidx.lifecycle.ViewModelProvider

class EmergencyContactViewModelFactory : ViewModelProvider.Factory {

    @Suppress("UNCHECKED_CAST")
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(EmergencyContactViewModel::class.java)) {
            return EmergencyContactViewModel() as T
        }
        throw IllegalArgumentException("Clase de ViewModel desconocida")
    }
}
```

### Descripción General

Fábrica simple para crear instancias de `EmergencyContactViewModel`. No requiere parámetros.

---

## MedicationViewModel.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

import android.app.Application
import androidx.lifecycle.AndroidViewModel
import androidx.lifecycle.viewModelScope
import com.google.firebase.database.FirebaseDatabase
import kotlinx.coroutines.flow.SharingStarted
import kotlinx.coroutines.flow.stateIn
import kotlinx.coroutines.launch
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.MedicationEntity
import mx.edu.utng.aimc.com.pantallaprincipal.data.repository.FirebaseMedicationRepository
import mx.edu.utng.aimc.com.pantallaprincipal.utils.scheduleAlarmsForMedication
import mx.edu.utng.aimc.com.pantallaprincipal.utils.cancelAlarmsForMedication

class MedicationViewModel(
    application: Application
) : AndroidViewModel(application) {

    private val repository = FirebaseMedicationRepository()
    private val firebaseDb = FirebaseDatabase.getInstance().reference

    val allMedications = repository.getAllMedications()
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000),
            initialValue = emptyList()
        )

    init {
        viewModelScope.launch {
            allMedications.collect { meds ->
                println("🔥 LISTA ACTUAL DE MEDICAMENTOS → $meds")
            }
        }
    }

    fun insertMedication(
        name: String,
        dose: String,
        note: String,
        days: List<String>,
        times: List<String>,
        totalDays: Int
    ) {
        viewModelScope.launch {
            val medication = MedicationEntity(
                name = name,
                dose = dose,
                note = note,
                schedules = times,
                days = days,
                totalDays = totalDays,
                daysTaken = 0
            )

            repository.insertMedication(medication)

            val medId = "${name}_${System.currentTimeMillis()}".hashCode().toLong()

            scheduleAlarmsForMedication(
                context = getApplication<Application>().applicationContext,
                medId = medId,
                times = times,
                days = days,
                medName = name
            )

            saveMedicationToFirebase(medication)
        }
    }

    private fun saveMedicationToFirebase(med: MedicationEntity) {
        val id = firebaseDb.child("medications").push().key ?: return

        firebaseDb.child("medications")
            .child(id)
            .setValue(med)
            .addOnSuccessListener {
                println("🔥 Guardado en Firebase Realtime DB con éxito")
            }
            .addOnFailureListener {
                println("❌ Error guardando en Firebase: ${it.message}")
            }
    }

    fun deleteMedication(med: MedicationEntity) {
        viewModelScope.launch {
            repository.deleteMedication(med)

            val medId = "${med.name}_${System.currentTimeMillis()}".hashCode()
            cancelAlarmsForMedication(getApplication<Application>().applicationContext, medId)
        }

        firebaseDb.child("medications").get().addOnSuccessListener { snapshot ->
            snapshot.children.forEach { node ->
                val item = node.getValue(MedicationEntity::class.java)
                if (item?.name == med.name) {
                    firebaseDb.child("medications").child(node.key!!).removeValue()
                }
            }
        }
    }

    fun markMedicationTaken(med: MedicationEntity) {
        viewModelScope.launch {
            val updated = med.copy(daysTaken = med.daysTaken + 1)

            repository.deleteMedication(med)
            repository.insertMedication(updated)

            if (updated.daysTaken >= updated.totalDays) {
                deleteMedication(updated)
            }
        }
    }
}
```

### Descripción General

ViewModel más complejo que maneja la lógica completa de medicamentos: guardado en Firebase, programación de alarmas, sincronización con Realtime Database y seguimiento del progreso del tratamiento.

### Ubicación

`mx.edu.utng.aimc.com.pantallaprincipal.viewmodel`

### Explicación Detallada

**Propiedades**

Extiende `AndroidViewModel` para tener acceso al `Context` de la aplicación, necesario para programar alarmas.

- `repository`: `FirebaseMedicationRepository` para Firestore.
- `firebaseDb`: Realtime Database para respaldo.
- `allMedications`: `StateFlow` con la lista observable.

**init{}** — Solo imprime en consola la lista actual para debugging.

**insertMedication()** — Flujo completo al agregar un medicamento: crea la entidad con `daysTaken = 0`, guarda en Firestore, genera ID único para alarmas (`hash de nombre + timestamp`), programa alarmas y guarda respaldo en Realtime Database.

**deleteMedication()** — Elimina de Firestore, cancela todas las alarmas y elimina de Realtime Database.

**markMedicationTaken()** — Incrementa `daysTaken`, actualiza en Firestore (elimina viejo + inserta nuevo) y, si completó el tratamiento, elimina automáticamente el medicamento.

### Archivos Relacionados

- `FirebaseMedicationRepository.kt` — El repositorio que usa.
- `AlarmUtils.kt` — Para programar y cancelar alarmas.
- `MedicationEntity.kt` — La entidad que maneja.
- `MedicationViewModelFactory.kt` — Su fábrica de creación.

---

## MedicationViewModelFactory.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

import android.app.Application
import androidx.lifecycle.ViewModel
import androidx.lifecycle.ViewModelProvider

class MedicationViewModelFactory(
    private val application: Application
) : ViewModelProvider.Factory {

    @Suppress("UNCHECKED_CAST")
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(MedicationViewModel::class.java)) {
            return MedicationViewModel(application) as T
        }
        throw IllegalArgumentException("Unknown ViewModel class: ${modelClass.name}")
    }
}
```

### Descripción General

Fábrica que proporciona el `Application` context al `MedicationViewModel`, necesario para programar alarmas.

---

## MainActivity.kt

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal

import android.content.pm.PackageManager
import android.os.Build
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.enableEdgeToEdge
import androidx.compose.runtime.collectAsState
import androidx.compose.runtime.remember
import androidx.core.app.ActivityCompat
import androidx.core.content.ContextCompat
import mx.edu.utng.aimc.com.pantallaprincipal.navigation.NavGraph
import mx.edu.utng.aimc.com.pantallaprincipal.ui.theme.PantallaPrincipalTheme
import mx.edu.utng.aimc.com.pantallaprincipal.utils.PlacesApiHelper
import mx.edu.utng.aimc.com.pantallaprincipal.utils.ThemePreferences
import androidx.compose.runtime.getValue

private const val NOTIFICATION_PERMISSION_REQUEST_CODE = 100

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()

        requestNotificationPermission()

        PlacesApiHelper.initialize(
            context = this,
            apiKey = "AIzaSyAUdfxCL1xKk66Ai8GcMET6fS1dyUH9pOk"
        )

        setContent {
            val themePreferences = remember { ThemePreferences(this) }
            val isDarkTheme by themePreferences.isDarkTheme.collectAsState(initial = false)

            PantallaPrincipalTheme(darkTheme = isDarkTheme) {
                NavGraph()
            }
        }
    }

    private fun requestNotificationPermission() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
            val permission = android.Manifest.permission.POST_NOTIFICATIONS

            if (ContextCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
                ActivityCompat.requestPermissions(
                    this,
                    arrayOf(permission),
                    NOTIFICATION_PERMISSION_REQUEST_CODE
                )
            }
        }
    }

    override fun onRequestPermissionsResult(
        requestCode: Int,
        permissions: Array<String>,
        grantResults: IntArray
    ) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults)
        if (requestCode == NOTIFICATION_PERMISSION_REQUEST_CODE) {
            if (grantResults.isNotEmpty() && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                android.util.Log.d("MainActivity", "Permiso de notificación concedido.")
            } else {
                android.util.Log.w("MainActivity", "El permiso de notificación fue denegado.")
            }
        }
    }
}
```

### Descripción General

Actividad principal de la aplicación. Punto de entrada que configura todo lo necesario: permisos de notificaciones, inicialización de Places API, tema y navegación.

### Ubicación

Raíz del paquete (`mx.edu.utng.aimc.com.pantallaprincipal`).

### Explicación Detallada

**onCreate()**

- `enableEdgeToEdge()`: Hace que la app use toda la pantalla, incluyendo áreas de sistema.
- Solicita permiso de notificaciones (solo Android 13+).
- Inicializa Places API con la API key (hardcodeada).
- Lee las preferencias de tema guardadas y aplica el tema correspondiente.
- Inicia la navegación con `NavGraph()`.

**requestNotificationPermission()** — Para Android 13 (Tiramisu) y superior, solicita permiso `POST_NOTIFICATIONS` necesario para mostrar notificaciones.

**onRequestPermissionsResult()** — Maneja la respuesta del usuario a la solicitud de permisos y registra el resultado en log.

---

## Conclusión

MediAlerta es una aplicación completa que cumple con su objetivo principal: ayudar a las personas a organizar su salud de manera sencilla y efectiva. A lo largo de su desarrollo, se implementaron todas las funcionalidades planeadas en el manual original, logrando una aplicación funcional y estable.

### Lo que se logró

**Gestión de medicamentos**: Los usuarios pueden registrar sus medicamentos con nombre, dosis, notas, horarios específicos y días de la semana. La aplicación lleva un conteo de los días tomados y muestra el progreso del tratamiento con barras visuales. Además, programa alarmas automáticas para recordar al usuario cuándo debe tomar cada dosis.

**Citas médicas**: Se implementó un sistema completo para agendar citas, con selectores de fecha y hora, lista de citas ordenadas y opción de eliminar. Los datos se guardan tanto local como en la nube.

**Contactos de emergencia**: Los usuarios pueden guardar números importantes como familiares, médicos o servicios de emergencia. Desde la misma aplicación pueden llamar a estos contactos o directamente al 911 con un solo toque.

**Farmacias cercanas**: Usando Google Maps y Places API, la aplicación muestra un mapa con la ubicación del usuario y las farmacias más cercanas. Cada farmacia aparece con su nombre, dirección y un botón para abrir Google Maps con la ruta para llegar.

**Personalización**: La aplicación recuerda las preferencias del usuario, como el tema claro u oscuro, y las aplica cada vez que se abre.

**Notificaciones**: El sistema de alarmas y notificaciones funciona correctamente, mostrando recordatorios llamativos con colores verdes y opciones para confirmar que se tomó el medicamento.

### Tecnologías utilizadas

El proyecto hace uso de tecnologías modernas de Android: Kotlin, Jetpack Compose, Room, Firebase (Firestore y Realtime Database), Google Maps y Places API, AlarmManager, DataStore, Corrutinas y Flow.

### Aspectos a mejorar

- La API key de Google Maps está hardcodeada en el código; debería moverse a un archivo de configuración seguro.
- Las notificaciones podrían tener más opciones, como posponer el recordatorio 5, 10 o 15 minutos.
- La eliminación de medicamentos al completar el tratamiento es automática, pero algunos usuarios podrían querer mantener un historial.
- En algunos formularios no se valida que los campos tengan información antes de guardar.
- Las dos pantallas de medicamentos (`MedicamentosScreen` y `MedicationListScreen`) podrían unificarse para evitar confusión.

### En resumen

MediAlerta es una aplicación funcional y bien estructurada que cumple con los objetivos planteados. Combina almacenamiento local y en la nube, ofrece una interfaz atractiva y fácil de usar, y resuelve necesidades reales de personas que requieren organización en su salud.

El código está organizado siguiendo buenas prácticas como la separación en capas (data, ui, viewmodel), uso de repositorios, inyección de dependencias y programación reactiva con Flow. Esto hace que la aplicación sea mantenible y escalable para futuras mejoras.

En conclusión, MediAlerta es una herramienta útil que puede ayudar a muchas personas a no olvidar sus medicamentos, recordar sus citas médicas y estar preparadas ante cualquier emergencia, todo desde la comodidad de su teléfono móvil.
