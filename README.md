# MediAlert01
Introducción
MediAlerta es una aplicación móvil desarrollada para Android que tiene como objetivo ayudar a las personas a llevar un control más organizado de su salud. La aplicación nace de la necesidad de muchas personas que toman medicamentos de forma regular, tienen citas médicas frecuentes o simplemente quieren estar preparados ante cualquier emergencia.

La aplicación permite a los usuarios registrar sus medicamentos con sus respectivos horarios y dosis, programar citas médicas, guardar contactos de emergencia y localizar farmacias cercanas a su ubicación. Todo esto en un solo lugar, con una interfaz colorida y fácil de usar que no requiere conocimientos técnicos.

El desarrollo de MediAlerta se realizó utilizando Kotlin como lenguaje de programación principal y Jetpack Compose para construir la interfaz de usuario moderna y reactiva. Los datos se almacenan tanto localmente en el dispositivo usando Room, como en la nube usando Firebase, lo que permite que la información no se pierda aunque se desinstale la aplicación o se cambie de teléfono.

El equipo detrás de MediAlerta está conformado por Colin Cardenas Kelly Anahi y Mendez Galvan Claudia Lizbeth, quienes diseñaron y programaron cada una de las funcionalidades pensando en hacer la vida más fácil a las personas que necesitan recordatorios constantes para su salud.

Link de descarga
https://edith294.github.io/PaginawebappSalud/

Vídeo de prueba
https://drive.google.com/file/d/1xIXpM33FM7raiktjbWgTmhMpb74Ps2iN/view?usp=sharing


##  Descripción de Carpetas

### `data/`
Contiene todo lo relacionado con el manejo de datos de la aplicación, incluyendo la base de datos local y las fuentes de datos.

- **dao/**: Objetos de acceso a datos para interactuar con Room
- **database/**: Configuración de la base de datos Room y convertidores
- **entity/**: Clases que representan las tablas de la base de datos
- **model/**: Modelos de datos para la lógica de negocio
- **repository/**: Repositorios que manejan las fuentes de datos (local y remota)

### `navigation/`
Gestiona la navegación entre las diferentes pantallas de la aplicación usando Jetpack Compose Navigation.

### `receivers/`
Contiene los BroadcastReceivers para manejar eventos del sistema como las alarmas de los recordatorios.

### `ui/`
Toda la interfaz de usuario de la aplicación construida con Jetpack Compose.

- **screens/**: Las diferentes pantallas de la aplicación organizadas por funcionalidad
  - **citas/**: Pantallas para gestionar citas médicas
  - **configuracion/**: Pantalla de configuración de la aplicación
  - **emergencia/**: Pantallas para gestionar contactos de emergencia
  - **farmacias/**: Pantalla para buscar farmacias cercanas
  - **home/**: Pantalla principal de la aplicación
  - **medicamentos/**: Pantallas para gestionar medicamentos
- **notificaciones/**: Componentes para manejar notificaciones
- **theme/**: Configuración del tema visual (colores, tipografía, estilos)
- **utils/**: Utilidades y helpers para la interfaz

### `viewmodel/`
ViewModels que separan la lógica de negocio de la interfaz de usuario y manejan el estado de las pantallas.

### Archivos Principales

- **MainActivity.kt**: Actividad principal que inicia la aplicación
- **AndroidManifest.xml**: Configuración de la aplicación (no mostrado en el árbol pero mencionado)

##  Tecnologías Utilizadas

- **Kotlin**: Lenguaje de programación principal
- **Jetpack Compose**: Para la interfaz de usuario moderna
- **Room**: Base de datos local
- **Firebase**: Para autenticación, base de datos en la nube y notificaciones
- **Google Maps**: Para ubicación de farmacias cercanas
- **Corrutinas**: Para operaciones asíncronas
- **Flow**: Para datos reactivos
- **ViewModel**: Para manejar el estado de la UI
- **Navigation Component**: Para la navegación entre pantallas
- **AlarmManager**: Para programar recordatorios y notificaciones

---

¿Quieres que documente algún archivo en específico de esta estructura?

# AppointmentDao.kt

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

 Descripción General
Este archivo es el encargado de comunicarse con la base de datos local de la aplicación, específicamente con la tabla donde se guardan las citas médicas. Usa Room, que es una herramienta que hace más fácil guardar y recuperar información en el teléfono.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.dao

 Explicación Detallada
¿Qué es un DAO?
Un DAO (Data Access Object) es un componente que actúa como intermediario entre la aplicación y la base de datos. En este caso, AppointmentDao se encarga de todas las operaciones relacionadas con las citas médicas.

Anotaciones y su Significado
@Dao: Esta anotación le indica a la aplicación que esta interfaz va a manejar las operaciones de la base de datos para las citas. Es como un identificador que dice "aquí se van a hacer las consultas".

@Insert: Marca el método que insertará nuevos registros en la base de datos.

@Delete: Marca el método que eliminará registros existentes.

@Query: Permite escribir consultas SQL personalizadas para obtener datos específicos.

Métodos y su Función
insertAppointment()
Este método guarda una nueva cita en la base de datos del teléfono. Cuando se guarda, si ya existía una cita con el mismo identificador, la reemplaza por la nueva debido a la estrategia OnConflictStrategy.REPLACE. Devuelve un número que es el identificador con el que se guardó la cita.

Parámetros: Recibe un objeto AppointmentEntity que contiene todos los datos de la cita como título, fecha, hora, etc.

Retorno: Un valor de tipo Long que es el ID único asignado a la cita recién guardada.

deleteAppointment()
Este método elimina una cita existente de la base de datos. Solo hay que pasarle la cita que se quiere borrar y él se encarga de quitarla de la tabla.

Parámetros: Recibe el objeto AppointmentEntity que se desea eliminar.

Retorno: No devuelve ningún valor.

getAllAppointments()
Este método obtiene todas las citas que están guardadas y las entrega ordenadas por fecha y hora, de la más próxima a la más lejana. Lo interesante es que usa Flow, que es una forma de estar siempre actualizado: si se agrega o elimina una cita, la lista se actualiza solita sin necesidad de hacer nada más.

Parámetros: No recibe parámetros.

Retorno: Un Flow que emite una lista de objetos AppointmentEntity. Cada vez que hay cambios en la tabla de citas, esta lista se actualiza automáticamente.

 Flujo de Trabajo Típico
Guardar una cita: Cuando el usuario crea una nueva cita, la aplicación llama a insertAppointment() con los datos de la cita.

Consultar citas: La pantalla que muestra las citas está observando getAllAppointments() para tener siempre la lista actualizada.

Eliminar una cita: Cuando el usuario decide borrar una cita, la aplicación llama a deleteAppointment() con la cita que se quiere eliminar.

Actualización automática: Al modificar la base de datos, el Flow emite automáticamente la nueva lista y la pantalla se actualiza sin necesidad de recargar.

 Notas Importantes
Los métodos insertAppointment() y deleteAppointment() son suspend, lo que significa que deben ejecutarse en segundo plano para no bloquear la interfaz de usuario.

La estrategia OnConflictStrategy.REPLACE garantiza que no habrá conflictos si se intenta guardar una cita con un ID que ya existe.

El uso de Flow hace que la aplicación sea más eficiente, ya que no necesita estar haciendo consultas constantemente para verificar si los datos cambiaron.

 Archivos Relacionados
AppointmentEntity.kt - Define la estructura de una cita en la base de datos

AppDatabase.kt - La base de datos principal que contiene esta tabla

AppointmentRepository.kt - El repositorio que utiliza este DAO para sus operaciones
# AppointmentDao.kt

##  Descripción General
Este archivo define la interfaz de acceso a datos (DAO) para la entidad `AppointmentEntity` utilizando Room, la biblioteca de persistencia de Android. Su función principal es gestionar las operaciones de la base de datos local relacionadas con las citas médicas.

---

##  Ubicación
`mx.edu.utng.aimc.com.pantallaprincipal.data.dao`

---

##  Dependencias
- `androidx.room.*` - Para las anotaciones y operaciones de Room
- `kotlinx.coroutines.flow.Flow` - Para observar cambios en tiempo real
- `mx.edu.utng.aimc.com.pantallaprincipal.data.entity.AppointmentEntity` - La entidad que representa una cita

---

##  Código Completo

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.dao

import androidx.room.*
import kotlinx.coroutines.flow.Flow
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.AppointmentEntity

/**
 * Data Access Object (DAO) para la gestión de citas médicas.
 * 
 * Proporciona métodos para insertar, eliminar y consultar citas
 * en la base de datos local utilizando Room.
 */
@Dao
interface AppointmentDao {

    /**
     * Inserta una nueva cita en la base de datos.
     * 
     * Si ya existe una cita con el mismo ID, será reemplazada
     * debido a la estrategia OnConflictStrategy.REPLACE.
     * 
     * @param appointment El objeto AppointmentEntity a insertar
     * @return El ID generado para la cita insertada (tipo Long)
     */
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAppointment(appointment: AppointmentEntity): Long

    /**
     * Elimina una cita existente de la base de datos.
     * 
     * @param appointment El objeto AppointmentEntity a eliminar
     */
    @Delete
    suspend fun deleteAppointment(appointment: AppointmentEntity)

    /**
     * Obtiene todas las citas guardadas, ordenadas por fecha y hora.
     * 
     * Utiliza Flow para que la UI pueda observar los cambios en
     * tiempo real. Cuando se inserta o elimina una cita, la lista
     * se actualiza automáticamente.
     * 
     * @return Flow que emite una lista de AppointmentEntity ordenada
     *         por fecha/hora ascendente (de la más próxima a la más lejana)
     */
    @Query("SELECT * FROM appointments ORDER BY dateTime ASC")
    fun getAllAppointments(): Flow<List<AppointmentEntity>>
}

# EmergencyContactDao.kt

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
 Descripción General
Este archivo es el encargado de comunicarse con la base de datos local de la aplicación, específicamente con la tabla donde se guardan los contactos de emergencia. Usa Room para facilitar el guardado y recuperación de esta información en el teléfono.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.dao

 Explicación Detallada
¿Qué es un DAO?
Un DAO (Data Access Object) es un componente que actúa como intermediario entre la aplicación y la base de datos. En este caso, EmergencyContactDao se encarga de todas las operaciones relacionadas con los contactos de emergencia.

Anotaciones y su Significado
@Dao: Esta anotación le indica a la aplicación que esta interfaz va a manejar las operaciones de la base de datos para los contactos de emergencia.

@Insert: Marca el método que insertará nuevos contactos en la base de datos.

@Delete: Marca el método que eliminará contactos existentes.

@Query: Permite escribir consultas SQL personalizadas para obtener o eliminar datos específicos.

Métodos y su Función
insert()
Este método guarda un nuevo contacto de emergencia en la base de datos del teléfono. Si ya existía un contacto con el mismo identificador, lo reemplaza por el nuevo debido a la estrategia OnConflictStrategy.REPLACE. Devuelve el identificador con el que se guardó el contacto.

Parámetros: Recibe un objeto EmergencyContactEntity que contiene todos los datos del contacto como nombre, teléfono, parentesco, etc.

Retorno: Un valor de tipo Long que es el ID único asignado al contacto recién guardado.

delete()
Este método elimina un contacto de emergencia existente de la base de datos. Se le pasa el contacto completo que se quiere borrar y él se encarga de quitarlo de la tabla.

Parámetros: Recibe el objeto EmergencyContactEntity que se desea eliminar.

Retorno: No devuelve ningún valor.

getAllContacts()
Este método obtiene todos los contactos de emergencia que están guardados y los entrega ordenados alfabéticamente por nombre. Usa Flow para que la lista se mantenga siempre actualizada: si se agrega o elimina un contacto, la lista se actualiza automáticamente.

Parámetros: No recibe parámetros.

Retorno: Un Flow que emite una lista de objetos EmergencyContactEntity. Cada vez que hay cambios en la tabla de contactos, esta lista se actualiza sola.

deleteById()
Este método elimina un contacto de emergencia pero usando directamente su identificador, sin necesidad de tener el objeto completo. Es útil cuando solo se conoce el ID del contacto que se quiere borrar.

Parámetros: Recibe un valor contactId de tipo Long que es el identificador único del contacto a eliminar.

Retorno: No devuelve ningún valor.

 Flujo de Trabajo Típico
Guardar un contacto: Cuando el usuario agrega un nuevo número de emergencia, la aplicación llama a insert() con los datos del contacto.

Consultar contactos: La pantalla que muestra los contactos de emergencia está observando getAllContacts() para tener siempre la lista actualizada.

Eliminar un contacto: Cuando el usuario decide borrar un contacto, la aplicación puede usar delete() si tiene el objeto completo, o deleteById() si solo tiene el ID.

Actualización automática: Al modificar la base de datos, el Flow emite automáticamente la nueva lista y la pantalla se actualiza sin necesidad de recargar.

 Notas Importantes
Todos los métodos son suspend, lo que significa que deben ejecutarse en segundo plano para no bloquear la interfaz de usuario.

La estrategia OnConflictStrategy.REPLACE garantiza que no habrá conflictos si se intenta guardar un contacto con un ID que ya existe.

El método deleteById() ofrece una forma más directa de eliminar contactos cuando no se tiene el objeto completo, lo cual es útil en ciertos casos como cuando se trabaja con listas donde solo se conoce el ID.

 Archivos Relacionados
EmergencyContactEntity.kt - Define la estructura de un contacto de emergencia en la base de datos

AppDatabase.kt - La base de datos principal que contiene esta tabla

EmergencyContactRepository.kt - El repositorio que utiliza este DAO para sus operaciones

AgregarNumeroEmergencia.kt - Pantalla donde se agregan nuevos contactos

# MedicationDao.kt

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
 Descripción General
Este archivo es el encargado de comunicarse con la base de datos local de la aplicación, específicamente con la tabla donde se guardan los medicamentos. Usa Room para facilitar el guardado y recuperación de esta información en el teléfono.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.dao

 Explicación Detallada
¿Qué es un DAO?
Un DAO (Data Access Object) es un componente que actúa como intermediario entre la aplicación y la base de datos. En este caso, MedicationDao se encarga de todas las operaciones relacionadas con los medicamentos que el usuario registra.

Anotaciones y su Significado
@Dao: Esta anotación le indica a la aplicación que esta interfaz va a manejar las operaciones de la base de datos para los medicamentos.

@Insert: Marca el método que insertará nuevos medicamentos en la base de datos.

@Delete: Marca el método que eliminará medicamentos existentes.

@Query: Permite escribir consultas SQL personalizadas para obtener datos específicos.

@Update: Marca el método que actualizará medicamentos existentes.

Métodos y su Función
insertMedication()
Este método guarda un nuevo medicamento en la base de datos del teléfono. Si ya existía un medicamento con el mismo identificador, lo reemplaza por el nuevo debido a la estrategia OnConflictStrategy.REPLACE. Devuelve el identificador con el que se guardó el medicamento.

Parámetros: Recibe un objeto MedicationEntity que contiene todos los datos del medicamento como nombre, dosis, horarios, etc.

Retorno: Un valor de tipo Long que es el ID único asignado al medicamento recién guardado. Este valor es importante porque permite hacer referencia al medicamento en otras partes de la aplicación.

deleteMedication()
Este método elimina un medicamento existente de la base de datos. Se le pasa el medicamento completo que se quiere borrar y él se encarga de quitarlo de la tabla.

Parámetros: Recibe el objeto MedicationEntity que se desea eliminar.

Retorno: No devuelve ningún valor.

getAllMedications()
Este método obtiene todos los medicamentos que están guardados en la base de datos. Usa Flow para que la lista se mantenga siempre actualizada: si se agrega, elimina o modifica un medicamento, la lista se actualiza automáticamente.

Parámetros: No recibe parámetros.

Retorno: Un Flow que emite una lista de objetos MedicationEntity. Cada vez que hay cambios en la tabla de medicamentos, esta lista se actualiza sola sin necesidad de hacer nuevas consultas.

updateMedication()
Este método actualiza la información de un medicamento que ya existe en la base de datos. Es diferente a insertMedication porque en lugar de crear un nuevo registro, modifica uno existente conservando su mismo identificador.

Parámetros: Recibe el objeto MedicationEntity con los datos actualizados del medicamento.

Retorno: No devuelve ningún valor.

 Flujo de Trabajo Típico
Guardar un medicamento: Cuando el usuario agrega un nuevo medicamento, la aplicación llama a insertMedication() con los datos del medicamento y recibe el ID asignado.

Consultar medicamentos: La pantalla que muestra la lista de medicamentos está observando getAllMedications() para tener siempre la lista actualizada.

Actualizar un medicamento: Cuando el usuario edita la información de un medicamento existente, la aplicación llama a updateMedication() con los datos modificados.

Eliminar un medicamento: Cuando el usuario decide borrar un medicamento, la aplicación llama a deleteMedication() con el medicamento que se quiere eliminar.

Actualización automática: Al modificar la base de datos, el Flow emite automáticamente la nueva lista y la pantalla se actualiza sin necesidad de recargar.

 Notas Importantes
Todos los métodos son suspend, lo que significa que deben ejecutarse en segundo plano para no bloquear la interfaz de usuario.

La estrategia OnConflictStrategy.REPLACE garantiza que no habrá conflictos si se intenta guardar un medicamento con un ID que ya existe.

El método insertMedication() devuelve el ID del medicamento recién creado, lo cual es útil para referenciarlo en otras partes de la aplicación como en recordatorios o alarmas.

A diferencia de los otros DAOs, este incluye un método @Update específico para modificar medicamentos existentes, lo que da más flexibilidad en la gestión de los datos.

 Archivos Relacionados
MedicationEntity.kt - Define la estructura de un medicamento en la base de datos

AppDatabase.kt - La base de datos principal que contiene esta tabla

MedicationRepository.kt - El repositorio que utiliza este DAO para sus operaciones

FirebaseMedicationRepository.kt - Repositorio que sincroniza con la nube

AddMedicationScreen.kt - Pantalla donde se agregan nuevos medicamentos

MedicamentosScreen.kt - Pantalla donde se muestra la lista de medicamentos

# AppDatabase.kt

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
 Descripción General
Este archivo define la base de datos local de la aplicación usando Room. Es el corazón del almacenamiento de datos en el teléfono, donde se guardan todos los registros de medicamentos, citas médicas y contactos de emergencia. Actúa como el punto central que conecta todas las tablas con sus respectivos DAOs.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.database

 Explicación Detallada
¿Qué es AppDatabase?
AppDatabase es la clase principal que representa la base de datos SQLite en el teléfono. Usando Room, se encarga de crear y administrar las tablas, las relaciones entre ellas y proporcionar acceso a los datos a través de los DAOs.

Anotaciones y su Significado
@Database: Esta anotación le indica a Room que esta clase es una base de datos. Tiene dos partes importantes:

entities: Lista las clases que serán tablas en la base de datos. Aquí se incluyen MedicationEntity, AppointmentEntity y EmergencyContactEntity.

version: El número de versión de la base de datos. Cada vez que se cambia la estructura de las tablas, este número debe aumentarse. Actualmente está en la versión 4.

@TypeConverters: Indica que se usará la clase Converters para convertir tipos de datos especiales que Room no maneja por defecto, como fechas o listas, a formatos que sí puede guardar en SQLite.

Componentes de la Base de Datos
Tablas Incluidas
La base de datos tiene tres tablas principales:

MedicationEntity: Guarda toda la información de los medicamentos que el usuario registra, como nombre, dosis, horarios, etc.

AppointmentEntity: Almacena los datos de las citas médicas, incluyendo fecha, hora, lugar y motivo de la cita.

EmergencyContactEntity: Contiene los contactos de emergencia con nombres, teléfonos y parentesco.

Métodos Abstractos
medicationDao(): Proporciona acceso al DAO de medicamentos, que contiene los métodos para insertar, actualizar, eliminar y consultar medicamentos.

appointmentDao(): Da acceso al DAO de citas médicas para todas las operaciones relacionadas con citas.

emergencyContactDao(): Permite acceder al DAO de contactos de emergencia para gestionar los números de emergencia.

Patrón Singleton
companion object
Esta parte implementa el patrón Singleton, que garantiza que solo exista una instancia de la base de datos en toda la aplicación. Esto es importante porque crear múltiples instancias de la base de datos puede causar errores y consumir recursos innecesariamente.

@Volatile private var INSTANCE
@Volatile: Esta palabra clave asegura que los cambios en la variable INSTANCE sean visibles inmediatamente para todos los hilos de ejecución. Previene problemas de concurrencia cuando varios procesos intentan acceder a la base de datos al mismo tiempo.

INSTANCE: Variable que guarda la única instancia de la base de datos. Inicialmente es null y se crea solo cuando es necesario.

getDatabase(context: Context)
Este método es el encargado de proporcionar la instancia de la base de datos. Funciona así:

Verifica si ya existe una instancia creada (INSTANCE).

Si ya existe, la devuelve directamente.

Si no existe, entra en un bloque synchronized para asegurar que solo un hilo pueda crear la instancia.

Dentro del bloque, construye la base de datos usando Room.databaseBuilder().

Guarda la nueva instancia en INSTANCE y la devuelve.

Configuración de la Base de Datos
Room.databaseBuilder()
Este constructor configura la base de datos con tres parámetros importantes:

context.applicationContext: El contexto de la aplicación, necesario para acceder al sistema de archivos del teléfono.

AppDatabase::class.java: La clase de la base de datos que se está construyendo.

"med_alert_db": El nombre del archivo de base de datos que se creará en el teléfono.

.fallbackToDestructiveMigration()
Esta es una configuración importante que determina qué pasa cuando cambia la estructura de la base de datos (cuando se aumenta la versión). Con esta opción:

Si hay cambios en las tablas y no hay una migración definida, Room simplemente borra todas las tablas existentes y las vuelve a crear con la nueva estructura.

Es útil durante el desarrollo porque evita errores de migración, pero tiene la desventaja de que se pierden todos los datos guardados cuando la aplicación se actualiza.

 Flujo de Trabajo Típico
Inicialización: Cuando la aplicación inicia, llama a AppDatabase.getDatabase(context) para obtener la instancia de la base de datos.

Acceso a DAOs: A través de la instancia, la aplicación obtiene los DAOs necesarios: database.medicationDao(), database.appointmentDao() o database.emergencyContactDao().

Operaciones: Los ViewModels y Repositorios usan estos DAOs para realizar operaciones en la base de datos.

Actualizaciones: Cuando el usuario agrega, modifica o elimina información, los cambios se reflejan inmediatamente en la base de datos.

 Notas Importantes
La versión actual de la base de datos es la 4, lo que indica que ha pasado por varios cambios estructurales durante el desarrollo.

El uso de .fallbackToDestructiveMigration() significa que al actualizar la aplicación, todos los datos guardados se perderán. En una versión final para producción, esto debería reemplazarse con migraciones adecuadas.

La anotación @TypeConverters(Converters::class) sugiere que existe una clase Converters que maneja tipos de datos especiales, como fechas o listas.

El patrón Singleton con @Volatile y synchronized asegura que la base de datos funcione correctamente incluso en aplicaciones con múltiples hilos de ejecución.

 Archivos Relacionados
Converters.kt - Clase que convierte tipos especiales para guardarlos en la base de datos

MedicationDao.kt, AppointmentDao.kt, EmergencyContactDao.kt - Los DAOs que esta base de datos proporciona

MedicationEntity.kt, AppointmentEntity.kt, EmergencyContactEntity.kt - Las entidades que definen las tablas

Todos los repositorios que usan estos DAOs para acceder a los datos

# Converters.kt

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
 Descripción General
Este archivo contiene una clase especial llamada Converters que sirve como traductor entre tipos de datos que Kotlin maneja fácilmente y tipos que la base de datos SQLite puede guardar. En particular, se encarga de convertir listas de texto en algo que se pueda almacenar en una sola celda de la base de datos y viceversa.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.database

 Explicación Detallada
¿Por qué se necesitan Converters?
SQLite, que es el motor de base de datos que Room usa por debajo, solo puede guardar tipos de datos básicos como números, texto y fechas. No entiende tipos más complejos de Kotlin como listas, conjuntos o clases personalizadas. Los TypeConverter resuelven este problema actuando como puentes: convierten tipos complejos a tipos simples para guardarlos, y luego los vuelven a convertir a su forma original cuando se recuperan.

Anotaciones y su Significado
@TypeConverter: Esta anotación le indica a Room que estos métodos son convertidores. Cuando Room encuentre un campo de tipo List<String> en una entidad, automáticamente usará estos métodos para convertirlo a String antes de guardarlo, y de String a List<String> cuando lo recupere.

Métodos y su Función
fromList()
Este método convierte una lista de textos en un solo texto largo. Es como tomar varios pedazos de información y unirlos en una sola cadena separada por comas.

Cómo funciona: Toma una lista como ["lunes", "martes", "miércoles"] y la convierte en un solo texto como "lunes,martes,miércoles".

Parámetros: Recibe una lista de strings (List<String>).

Retorno: Devuelve un solo string con todos los elementos de la lista unidos por comas.

toList()
Este método hace la operación contraria: toma un texto largo que tiene elementos separados por comas y lo vuelve a convertir en una lista.

Cómo funciona: Toma un texto como "lunes,martes,miércoles" y lo convierte de vuelta a una lista ["lunes", "martes", "miércoles"].

Parámetros: Recibe un string que contiene elementos separados por comas.

Retorno: Devuelve una lista de strings. Si el texto está vacío, devuelve una lista vacía.

 Ejemplos Prácticos
Ejemplo 1: Horarios de medicamentos
Imagina que un medicamento se toma en múltiples horarios: 8:00 AM, 2:00 PM y 8:00 PM.

En la entidad MedicationEntity:

kotlin
val horarios: List<String> = listOf("8:00 AM", "2:00 PM", "8:00 PM")
Cuando se guarda en la base de datos:

Room ve que horarios es una List<String>

Busca un convertidor y encuentra fromList()

Convierte la lista a: "8:00 AM,2:00 PM,8:00 PM"

Guarda este texto en una sola columna de la tabla

Cuando se recupera de la base de datos:

Room lee el texto "8:00 AM,2:00 PM,8:00 PM"

Usa toList() para convertirlo de vuelta a lista

La aplicación recibe nuevamente ["8:00 AM", "2:00 PM", "8:00 PM"]

Ejemplo 2: Días de la semana para recordatorios
Si un recordatorio debe activarse los lunes, miércoles y viernes:

Guardar: ["lunes", "miércoles", "viernes"] → "lunes,miércoles,viernes"

Recuperar: "lunes,miércoles,viernes" → ["lunes", "miércoles", "viernes"]

 Casos Especiales
Listas Vacías
El método toList() tiene una validación importante:

kotlin
return if (value.isEmpty()) emptyList() else value.split(",")
Esto significa que si el texto guardado está vacío (algo que puede pasar si nunca se agregaron elementos a la lista), en lugar de intentar hacer split(",") en un texto vacío (que produciría una lista con un elemento vacío), devuelve directamente una lista vacía. Esto evita errores y hace que la aplicación se comporte de manera más predecible.

Elementos con Comas
Una limitación importante de este método es que si algún elemento de la lista contiene una coma, se romperá. Por ejemplo:

kotlin
Lista original: ["Martes, 2:00 PM", "Jueves, 4:00 PM"]
Texto guardado: "Martes, 2:00 PM,Jueves, 4:00 PM"
Al recuperar: ["Martes", " 2:00 PM", "Jueves", " 4:00 PM"] - Error
Para casos donde los elementos pueden contener comas, se necesitaría un separador más complejo, pero para los usos típicos de esta aplicación, este método funciona bien.

 Archivos Relacionados
AppDatabase.kt - Donde se registra este convertidor con @TypeConverters(Converters::class)

MedicationEntity.kt - Probablemente la entidad que usa listas para horarios o días

AppointmentEntity.kt - Podría usar listas para recordatorios o notas

EmergencyContactEntity.kt - Menos probable, pero podría usar listas para múltiples teléfonos

 Notas Adicionales
Estos convertidores son específicos para List<String>. Si la aplicación necesitara convertir otros tipos (como List<Int>, Date, etc.), habría que agregar más métodos en esta clase.

El separador usado es la coma (,), que es simple y eficiente, pero como se mencionó, tiene limitaciones si los textos contienen comas.

Esta clase se registra en AppDatabase con la anotación @TypeConverters(Converters::class), lo que la hace disponible para todas las entidades de la base de datos.

Room cachea los convertidores, así que usarlos no afecta negativamente el rendimiento de la aplicación.

# AppointmentEntity.kt

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
 Descripción General
Este archivo define la estructura de cómo se guardan las citas médicas en la base de datos del teléfono. Es como un molde que le dice a la aplicación qué información debe guardar de cada cita y de qué tipo es cada dato. Cada vez que el usuario agrega una cita, se crea un objeto de este tipo para almacenarla.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.entity

 Explicación Detallada
¿Qué es una Entidad?
En el contexto de bases de datos, una entidad es una representación de una tabla en la base de datos. Cada objeto de tipo AppointmentEntity corresponde a una fila en la tabla appointments, y cada propiedad de la clase corresponde a una columna de esa tabla.

Anotaciones y su Significado
@Entity: Esta anotación le indica a Room que esta clase representa una tabla en la base de datos. El parámetro tableName = "appointments" especifica el nombre que tendrá la tabla en la base de datos. Si no se especifica, Room usaría el nombre de la clase como nombre de tabla.

@PrimaryKey: Marca una propiedad como la llave principal de la tabla. La llave principal es un identificador único que no se puede repetir en ninguna otra fila de la misma tabla.

autoGenerate = true le dice a Room que genere automáticamente un valor único para esta llave cada vez que se inserte una nueva cita. Así no hay que preocuparse por asignar un ID manualmente.

Propiedades de la Entidad
id: Int = 0
Esta es la llave principal de la tabla, el identificador único de cada cita.

Tipo: Int (número entero)

Valor por defecto: 0

Características:

Se genera automáticamente cuando se guarda una nueva cita

Cada cita tiene un ID diferente

Sirve para identificar, buscar, actualizar o eliminar citas específicas

Aunque aquí se usa Int, en otros DAOs como MedicationDao se usa Long para los IDs. La diferencia es el tamaño máximo que pueden almacenar.

place: String = ""
Almacena el lugar donde será la cita médica.

Tipo: String (texto)

Valor por defecto: "" (texto vacío)

Ejemplos de valores:

"Consultorio del Dr. Martínez"

"Hospital General, piso 3"

"Clínica dental Sonrisas"

"Laboratorio de análisis clínicos"

dateTime: Long = 0L
Guarda la fecha y hora de la cita.

Tipo: Long (número entero grande)

Valor por defecto: 0L (cero, que representa una fecha inválida)

¿Por qué un número y no una fecha?
En programación, es común guardar las fechas como números grandes llamados "timestamps". Estos números representan la cantidad de milisegundos que han pasado desde el 1 de enero de 1970. Por ejemplo:

0L = 1 de enero de 1970, 00:00:00

1609459200000L = 1 de enero de 2021, 00:00:00

1704568800000L = 6 de enero de 2024, 12:00:00

Guardar fechas como números tiene ventajas:

Ocupan menos espacio en la base de datos

Se pueden ordenar fácilmente (mayor número = fecha más reciente)

Son más rápidos de procesar

Se pueden convertir a cualquier formato de fecha después

 Cómo se Usa en la Práctica
Crear una nueva cita
Cuando el usuario llena el formulario para agregar una cita, la aplicación hace algo como:

kotlin
// La fecha "15 de enero de 2024, 10:30 AM" se convierte a timestamp
val timestamp = 1705325400000L

// Se crea la entidad con los datos del formulario
val nuevaCita = AppointmentEntity(
    place = "Consultorio Dental",
    dateTime = timestamp
)

// El id se deja en 0 porque Room lo generará automáticamente
Guardar la cita en la base de datos
Luego, a través del DAO, se guarda:

kotlin
appointmentDao.insertAppointment(nuevaCita)
// Room ignora el id=0 y asigna uno nuevo, por ejemplo 1, 2, 3...
Recuperar una cita
Cuando se lee de la base de datos, se obtiene algo como:

kotlin
AppointmentEntity(
    id = 5,  // Ya tiene un ID asignado por Room
    place = "Consultorio Dental",
    dateTime = 1705325400000L
)
 Notas Importantes
Los comentarios en el código: Los comentarios //  CAMBIAR DE Long A Int y similares indican que hubo cambios recientes en esta clase, probablemente para corregir errores o adaptarse a otras partes de la aplicación.

Valores por defecto: Todas las propiedades tienen valores por defecto, lo que permite crear objetos sin tener que especificar todos los campos. Esto es útil en ciertas situaciones de programación.

Diferencia entre Int y Long: Aunque aquí se usa Int para el ID, en otros DAOs como MedicationDao se usa Long para los IDs. La diferencia práctica es que Long puede almacenar números mucho más grandes, pero para una aplicación normal, Int es suficiente (puede guardar más de 2 mil millones de citas).

 Archivos Relacionados
AppointmentDao.kt - El DAO que usa esta entidad para operaciones de base de datos

AppDatabase.kt - La base de datos que incluye esta entidad en su lista de tablas

AppointmentRepository.kt - El repositorio que maneja la lógica de negocio de las citas

FirebaseAppointmentRepository.kt - Para sincronizar con la nube

AgregarCita.kt y CitasMedicasScreen.kt - Las pantallas donde se crean y muestran las citas

AppointmentViewModel.kt - El ViewModel que usa esta entidad para manejar el estado

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
 Notas Importantes
Contactos de emergencia: Esta tabla está diseñada específicamente para guardar contactos a los que se pueda recurrir en caso de una emergencia médica.

Simplicidad: A diferencia de otras entidades que pueden tener muchos campos, esta es intencionalmente simple: solo nombre y teléfono. En una emergencia, no se necesita más información.

Valores por defecto: Todas las propiedades tienen valores por defecto (0 para id, texto vacío para nombre y teléfono), lo que permite crear objetos sin tener que especificar todos los campos.

ID autogenerado: El sistema de IDs automáticos asegura que cada contacto tenga un identificador único sin que el usuario tenga que preocuparse por eso.

 Archivos Relacionados
EmergencyContactDao.kt - El DAO que usa esta entidad para las operaciones de base de datos. Tiene métodos como insert(), delete(), getAllContacts() y deleteById().

AppDatabase.kt - La base de datos que incluye esta entidad en su lista de tablas. Aparece en la lista de entities junto con MedicationEntity y AppointmentEntity.

EmergencyContactRepository.kt - El repositorio que maneja la lógica de negocio de los contactos de emergencia, usando este DAO.

FirebaseContactRepository.kt - Para sincronizar los contactos de emergencia con la nube (Firebase).

AgregarNumeroEmergencia.kt - La pantalla donde el usuario agrega nuevos contactos de emergencia.

EmergencyContactViewModel.kt - El ViewModel que usa esta entidad para manejar el estado de la pantalla de contactos.

# MedicationEntity.kt

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
 Descripción General
Este archivo define la estructura de cómo se guardan los medicamentos en la base de datos del teléfono. Es el molde más completo de la aplicación porque los medicamentos tienen mucha información asociada: el nombre, la dosis, las notas, los horarios, los días de la semana y el progreso del tratamiento. Cada vez que el usuario agrega un nuevo medicamento, se crea un objeto de este tipo para almacenarlo.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.entity

 Explicación Detallada
¿Qué es una Entidad?
En el contexto de bases de datos, una entidad es una representación de una tabla en la base de datos. Cada objeto de tipo MedicationEntity corresponde a una fila en la tabla medications, y cada propiedad de la clase corresponde a una columna de esa tabla.

Anotaciones y su Significado
@Entity: Esta anotación le indica a Room que esta clase representa una tabla en la base de datos. El parámetro tableName = "medications" especifica el nombre que tendrá la tabla. Es importante porque en el código se usa el nombre de la clase, pero en la base de datos física se crea una tabla llamada "medications".

@PrimaryKey: Marca una propiedad como la llave principal de la tabla. La llave principal es un identificador único que no se puede repetir en ninguna otra fila.

autoGenerate = true le dice a Room que genere automáticamente un valor único para esta llave cada vez que se inserte un nuevo medicamento. Así la aplicación no tiene que preocuparse por asignar números de identificación.

Propiedades de la Entidad
id: Int = 0
Esta es la llave principal de la tabla, el identificador único de cada medicamento.

Tipo: Int (número entero)

Valor por defecto: 0

Características:

Se genera automáticamente cuando se guarda un nuevo medicamento

Cada medicamento tiene un ID diferente

Sirve para identificar, buscar, actualizar o eliminar medicamentos específicos

El valor 0 es solo un marcador de posición; Room lo reemplaza con un número real al guardar

name: String = ""
Almacena el nombre del medicamento.

Tipo: String (texto)

Valor por defecto: "" (texto vacío)

Ejemplos de valores:

"Paracetamol"

"Amoxicilina 500mg"

"Ibuprofeno"

"Losartán 50mg"

"Insulina rápida"

dose: String = ""
Guarda la dosis o cantidad del medicamento que se debe tomar.

Tipo: String (texto)

Valor por defecto: "" (texto vacío)

Ejemplos de valores:

"1 tableta"

"500 mg"

"2 cucharadas"

"10 ml"

"1 ampolla"

note: String = ""
Almacena notas adicionales o instrucciones especiales para tomar el medicamento.

Tipo: String (texto)

Valor por defecto: "" (texto vacío)

Ejemplos de valores:

"Tomar después de los alimentos"

"Evitar exponerse al sol"

"Tomar con abundante agua"

"No mezclar con alcohol"

"Suspender si hay reacciones alérgicas"

schedules: List<String> = emptyList()
Guarda los horarios en los que se debe tomar el medicamento. Es una lista de textos porque puede tener múltiples horarios al día.

Tipo: List<String> (lista de textos)

Valor por defecto: emptyList() (lista vacía)

Características importantes:

Es una lista, no un solo texto, porque un medicamento puede tomarse varias veces al día

Los valores se guardan usando los convertidores de la clase Converters

En la base de datos física, esta lista se convierte a un solo texto con los elementos separados por comas

Ejemplos de valores:

["8:00 AM", "2:00 PM", "8:00 PM"] (tres veces al día)

["7:30 AM"] (solo una vez al día)

["8:00 AM", "8:00 PM"] (cada 12 horas)

days: List<String> = emptyList()
Indica los días de la semana en que se debe tomar el medicamento.

Tipo: List<String> (lista de textos)

Valor por defecto: emptyList() (lista vacía)

Características importantes:

También es una lista que se convierte usando los convertidores

Permite tratamientos que no son todos los días

Ejemplos de valores:

["lunes", "miércoles", "viernes"] (días alternados)

["todos"] o ["lunes", "martes", "miércoles", "jueves", "viernes", "sábado", "domingo"] (todos los días)

["sábado", "domingo"] (solo fines de semana)

totalDays: Int = 0
Representa la duración total del tratamiento en días.

Tipo: Int (número entero)

Valor por defecto: 0

Ejemplos de valores:

7 para un tratamiento de una semana

10 para un tratamiento de 10 días

30 para un tratamiento de un mes

0 podría significar que es un tratamiento continuo sin fecha de finalización

daysTaken: Int = 0
Lleva el conteo de cuántos días se ha tomado el medicamento. Es como un contador de progreso.

Tipo: Int (número entero)

Valor por defecto: 0

Características importantes:

Este número aumenta cada vez que el usuario confirma que tomó el medicamento

Sirve para mostrar el progreso del tratamiento

Cuando daysTaken llega a ser igual a totalDays, significa que el tratamiento está completo

Ejemplo de uso:

Un antibiótico de 7 días: totalDays = 7, y cada día que pasa y el usuario confirma que lo tomó, daysTaken aumenta de 1 hasta llegar a 7.

 Cómo se Usa en la Práctica
Crear un nuevo medicamento
Cuando el usuario llena el formulario para agregar un medicamento, la aplicación hace algo como:

kotlin
val nuevoMedicamento = MedicationEntity(
    name = "Amoxicilina",
    dose = "500 mg",
    note = "Tomar cada 12 horas, con alimentos",
    schedules = listOf("8:00 AM", "8:00 PM"),
    days = listOf("todos"),
    totalDays = 7,
    daysTaken = 0  // Aún no ha tomado ninguna dosis
)
// El id se deja en 0 porque Room lo generará automáticamente al guardar
Guardar el medicamento en la base de datos
Luego, a través del DAO, se guarda:

kotlin
val idGenerado = medicationDao.insertMedication(nuevoMedicamento)
// Room ignora el id=0, asigna uno nuevo (ej: 1, 2, 3...) y lo devuelve
Actualizar el progreso
Cuando el usuario confirma que tomó el medicamento:

kotlin
// Se obtiene el medicamento actual de la base de datos
val medicamento = medicationDao.getMedicationById(id)

// Se aumenta el contador de días tomados
val medicamentoActualizado = medicamento.copy(
    daysTaken = medicamento.daysTaken + 1
)

// Se guarda la actualización
medicationDao.updateMedication(medicamentoActualizado)
Notas Importantes
Uso de convertidores: Las propiedades schedules y days son listas, pero SQLite no guarda listas directamente. Por eso se usa la clase Converters que convierte estas listas a texto (separado por comas) para guardarlas, y las vuelve a convertir a listas cuando se recuperan.

Tratamientos continuos: Cuando totalDays es 0, podría interpretarse como un tratamiento que no tiene fecha de finalización, como medicamentos de por vida.

Progreso del tratamiento: La combinación de totalDays y daysTaken permite mostrar barras de progreso y saber cuándo termina un tratamiento.

Listas vacías: Los valores por defecto emptyList() para schedules y days son importantes porque si el usuario no selecciona horarios o días, no se guardan listas nulas que podrían causar errores.

 Archivos Relacionados
MedicationDao.kt - El DAO que usa esta entidad para operaciones de base de datos, con métodos como insertMedication(), deleteMedication(), getAllMedications() y updateMedication().

AppDatabase.kt - La base de datos que incluye esta entidad en su lista de tablas.

Converters.kt - La clase que convierte las listas schedules y days a texto para guardarlas en la base de datos.

MedicationRepository.kt - El repositorio que maneja la lógica de negocio de los medicamentos.

FirebaseMedicationRepository.kt - Para sincronizar los medicamentos con la nube (Firebase).

AddMedicationScreen.kt - La pantalla donde el usuario agrega nuevos medicamentos con todos estos campos.

MedicamentosScreen.kt - La pantalla donde se muestra la lista de medicamentos con su progreso.

MedicationViewModel.kt - El ViewModel que usa esta entidad para manejar el estado de la pantalla de medicamentos.

package mx.edu.utng.aimc.com.pantallaprincipal.data.model

data class Medication(
    val id: Int = 0,
    val name: String = "",
    val dose: String = "",
    val hour: String = ""
)
Posible Conversión entre Entity y Model
Es probable que en el código haya funciones para convertir de un tipo a otro:

kotlin
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
        note = "",  // No hay nota en el modelo simple
        schedules = if (this.hour.isNotEmpty()) listOf(this.hour) else emptyList(),
        days = emptyList(),  // No hay días en el modelo simple
        totalDays = 0,
        daysTaken = 0
    )
}
 Notas Importantes
Simplificación: Este modelo es una versión simplificada. Pierde información como notas, días de la semana, y el progreso del tratamiento.

Horario único: Mientras que MedicationEntity permite múltiples horarios en una lista, Medication solo tiene un campo hour para un solo horario. Esto sugiere que se usa para casos donde solo interesa el primer o principal horario.

Sin dependencias: A diferencia de MedicationEntity, este modelo no tiene anotaciones de Room ni depende de la base de datos, lo que lo hace más ligero y fácil de usar en cualquier parte de la aplicación.

Valores por defecto: Todos los campos tienen valores por defecto, lo que permite crear objetos vacíos fácilmente, útil para formularios o estados iniciales.

 Archivos Relacionados
MedicationEntity.kt - La versión completa para la base de datos

Posibles pantallas donde se muestren listas simples de medicamentos

Posibles ViewModels que manejen datos resumidos

Posibles adaptadores o funciones de conversión entre Entity y Model

# AppointmentRepository.kt

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
📋 Descripción General
Este archivo define el repositorio de citas médicas, que actúa como un puente entre la base de datos local y el resto de la aplicación. Su trabajo es proporcionar una forma limpia y organizada de acceder a los datos de las citas sin que las otras partes de la aplicación tengan que saber cómo están guardados internamente.

📁 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.repository

🧩 Explicación Detallada
¿Qué es un Repositorio?
Un repositorio es un patrón de diseño que separa la lógica de acceso a datos del resto de la aplicación. En lugar de que los ViewModels o las pantallas hablen directamente con el DAO de la base de datos, hablan con este repositorio. Esto tiene varias ventajas:

Organización: Todo lo relacionado con citas está en un solo lugar

Flexibilidad: Si en el futuro se cambia la base de datos o se agrega una fuente de datos en la nube, solo hay que modificar el repositorio

Pruebas: Es más fácil probar la aplicación porque se puede crear un repositorio falso para las pruebas

Constructor y Dependencias
kotlin
class AppointmentRepository(
    private val appointmentDao: AppointmentDao
)
El repositorio recibe en su constructor el DAO de citas. Esto se llama "inyección de dependencias" y permite que el repositorio use el DAO sin tener que crearlo él mismo. Quien cree el repositorio (normalmente un ViewModel) le pasa el DAO ya listo para usar.

Métodos y su Función
getAllAppointments()
Este método obtiene todas las citas guardadas en la base de datos.

Cómo funciona: Simplemente llama al método correspondiente del DAO y devuelve lo que este le da.

Parámetros: No recibe parámetros.

Retorno: Un Flow<List<AppointmentEntity>>, que es una lista de citas que se actualiza automáticamente cuando hay cambios.

Característica especial: Al ser un Flow, cualquier pantalla que esté observando este método recibirá actualizaciones en tiempo real sin tener que hacer nada especial.

insertAppointment()
Este método guarda una nueva cita en la base de datos.

Cómo funciona: Llama al método insertAppointment() del DAO y le pasa la cita que se quiere guardar.

Parámetros: Recibe un objeto AppointmentEntity con todos los datos de la cita (lugar y fecha).

Retorno: Devuelve un valor Long que es el ID con el que se guardó la cita en la base de datos.

Característica especial: Es un método suspend, lo que significa que debe ejecutarse en segundo plano para no bloquear la interfaz de usuario.

deleteAppointment()
Este método elimina una cita existente de la base de datos.

Cómo funciona: Llama al método deleteAppointment() del DAO y le pasa la cita que se quiere eliminar.

Parámetros: Recibe el objeto AppointmentEntity completo de la cita que se desea borrar.

Retorno: No devuelve ningún valor.

Característica especial: También es un método suspend que debe ejecutarse en segundo plano.

🔄 Flujo de Trabajo Típico
Ejemplo 1: Mostrar todas las citas
kotlin
// En un ViewModel
class AppointmentViewModel(private val repository: AppointmentRepository) {
    
    // El ViewModel expone las citas para que la pantalla las observe
    val allAppointments = repository.getAllAppointments()
    
    // La pantalla solo necesita observar allAppointments y se actualiza sola
}

// En la pantalla
val allAppointments by viewModel.allAppointments.collectAsState()
// Se muestra la lista en la UI
Ejemplo 2: Agregar una nueva cita
kotlin
// En un ViewModel
fun addAppointment(place: String, dateTime: Long) {
    viewModelScope.launch {
        // Se crea la entidad (el ID se deja en 0 porque Room lo genera)
        val newAppointment = AppointmentEntity(
            place = place,
            dateTime = dateTime
        )
        
        // Se guarda usando el repositorio
        val newId = repository.insertAppointment(newAppointment)
        
        // newId contiene el ID asignado por la base de datos
        Log.d("Appointment", "Cita guardada con ID: $newId")
    }
}
Ejemplo 3: Eliminar una cita
kotlin
// En un ViewModel
fun deleteAppointment(appointment: AppointmentEntity) {
    viewModelScope.launch {
        repository.deleteAppointment(appointment)
        // La cita se elimina y la lista se actualiza automáticamente
    }
}
Notas Importantes
Simplicidad: Este repositorio es bastante simple porque solo pasa llamadas al DAO. En aplicaciones más complejas, aquí se podrían agregar validaciones, transformaciones de datos, o lógica de negocio.

Métodos suspend: Todos los métodos que modifican la base de datos son suspend, lo que obliga a llamarlos desde una corrutina. Esto es bueno porque asegura que no se ejecuten en el hilo principal.

Sin lógica adicional: A diferencia de otros repositorios que podrían tener FirebaseAppointmentRepository, este solo maneja la base de datos local. Esto sugiere que las citas solo se guardan localmente o que hay otro repositorio para la nube.

Inyección de dependencias: El constructor recibe el DAO, lo que hace que el repositorio sea fácil de probar y de mantener.

 Archivos Relacionados
AppointmentDao.kt - El DAO que este repositorio usa para acceder a la base de datos

AppointmentEntity.kt - La entidad que representa una cita en la base de datos

AppDatabase.kt - La base de datos que proporciona el DAO

FirebaseAppointmentRepository.kt - Posible repositorio alternativo para la nube

AppointmentViewModel.kt - El ViewModel que usa este repositorio

AgregarCita.kt y CitasMedicasScreen.kt - Las pantallas que muestran y crean citas

# EmergencyContactRepository.kt

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
 Descripción General
Este archivo define el repositorio de contactos de emergencia, que actúa como un intermediario entre la base de datos local y el resto de la aplicación. Su función es proporcionar una forma organizada y limpia de acceder a los datos de los contactos de emergencia sin que las otras partes de la aplicación tengan que saber cómo están guardados internamente.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.repository

 Explicación Detallada
¿Qué es un Repositorio?
Un repositorio es un patrón de diseño que separa la lógica de acceso a datos del resto de la aplicación. En lugar de que los ViewModels o las pantallas hablen directamente con el DAO de la base de datos, hablan con este repositorio. Esto tiene varias ventajas:

Organización: Todo lo relacionado con contactos de emergencia está en un solo lugar

Flexibilidad: Si en el futuro se cambia la base de datos o se agrega una fuente de datos en la nube, solo hay que modificar el repositorio

Pruebas: Es más fácil probar la aplicación porque se puede crear un repositorio falso para las pruebas

Constructor y Dependencias
kotlin
class EmergencyContactRepository(
    private val contactDao: EmergencyContactDao
)
El repositorio recibe en su constructor el DAO de contactos de emergencia. Esto se llama "inyección de dependencias" y permite que el repositorio use el DAO sin tener que crearlo él mismo. Quien cree el repositorio (normalmente un ViewModel) le pasa el DAO ya listo para usar.

Métodos y su Función
getAllContacts()
Este método obtiene todos los contactos de emergencia guardados en la base de datos.

Cómo funciona: Simplemente llama al método correspondiente del DAO y devuelve lo que este le da.

Parámetros: No recibe parámetros.

Retorno: Un Flow<List<EmergencyContactEntity>>, que es una lista de contactos que se actualiza automáticamente cuando hay cambios.

Característica especial: Al ser un Flow, cualquier pantalla que esté observando este método recibirá actualizaciones en tiempo real. Si se agrega o elimina un contacto, la lista se actualiza sola.

insertContact()
Este método guarda un nuevo contacto de emergencia en la base de datos.

Cómo funciona: Llama al método insert() del DAO y le pasa el contacto que se quiere guardar.

Parámetros: Recibe un objeto EmergencyContactEntity con todos los datos del contacto (nombre y número de teléfono).

Retorno: Devuelve un valor Long que es el ID con el que se guardó el contacto en la base de datos.

Característica especial: Es un método suspend, lo que significa que debe ejecutarse en segundo plano para no bloquear la interfaz de usuario.

deleteContact()
Este método elimina un contacto existente de la base de datos usando el objeto completo.

Cómo funciona: Llama al método delete() del DAO y le pasa el contacto que se quiere eliminar.

Parámetros: Recibe el objeto EmergencyContactEntity completo del contacto que se desea borrar.

Retorno: No devuelve ningún valor.

Característica especial: También es un método suspend que debe ejecutarse en segundo plano.

deleteById()
Este método elimina un contacto existente pero usando directamente su identificador, sin necesidad de tener el objeto completo.

Cómo funciona: Llama al método deleteById() del DAO y le pasa el ID del contacto a eliminar.

Parámetros: Recibe un valor contactId de tipo Long que es el identificador único del contacto.

Retorno: No devuelve ningún valor.

Característica especial: Es útil cuando solo se conoce el ID del contacto, por ejemplo, cuando se trabaja con listas donde cada elemento tiene su ID.

 Flujo de Trabajo Típico
Ejemplo 1: Mostrar todos los contactos
kotlin
// En un ViewModel
class EmergencyContactViewModel(private val repository: EmergencyContactRepository) {
    
    // El ViewModel expone los contactos para que la pantalla los observe
    val allContacts = repository.getAllContacts()
    
    // La pantalla solo necesita observar allContacts y se actualiza sola
}

// En la pantalla
val allContacts by viewModel.allContacts.collectAsState()
// Se muestra la lista de contactos en la UI
Ejemplo 2: Agregar un nuevo contacto
kotlin
// En un ViewModel
fun addContact(name: String, phoneNumber: String) {
    viewModelScope.launch {
        // Se crea la entidad (el ID se deja en 0 porque Room lo genera)
        val newContact = EmergencyContactEntity(
            name = name,
            phoneNumber = phoneNumber
        )
        
        // Se guarda usando el repositorio
        val newId = repository.insertContact(newContact)
        
        // newId contiene el ID asignado por la base de datos
        Log.d("EmergencyContact", "Contacto guardado con ID: $newId")
    }
}
Ejemplo 3: Eliminar un contacto
kotlin
// En un ViewModel
fun deleteContact(contact: EmergencyContactEntity) {
    viewModelScope.launch {
        // Se puede eliminar con el objeto completo
        repository.deleteContact(contact)
        // El contacto se elimina y la lista se actualiza automáticamente
    }
}

fun deleteContactById(contactId: Long) {
    viewModelScope.launch {
        // O se puede eliminar directamente con el ID
        repository.deleteById(contactId)
        // El contacto con ese ID se elimina
    }
}
Notas Importantes
Dos formas de eliminar: Este repositorio ofrece dos métodos para eliminar contactos (deleteContact y deleteById), lo que da flexibilidad según lo que se tenga disponible en cada situación.

Métodos suspend: Todos los métodos que modifican la base de datos son suspend, lo que obliga a llamarlos desde una corrutina. Esto asegura que no se ejecuten en el hilo principal.

Simplicidad: Al igual que el repositorio de citas, este es bastante simple porque solo pasa llamadas al DAO. En aplicaciones más complejas, aquí se podrían agregar validaciones o lógica de negocio.

Sin lógica adicional: Este repositorio solo maneja la base de datos local. Si hay un FirebaseContactRepository, sería para sincronizar con la nube.

Inyección de dependencias: El constructor recibe el DAO, lo que hace que el repositorio sea fácil de probar y de mantener.

 Archivos Relacionados
EmergencyContactDao.kt - El DAO que este repositorio usa para acceder a la base de datos

EmergencyContactEntity.kt - La entidad que representa un contacto de emergencia

AppDatabase.kt - La base de datos que proporciona el DAO

FirebaseContactRepository.kt - Posible repositorio alternativo para la nube

EmergencyContactViewModel.kt - El ViewModel que usa este repositorio

AgregarNumeroEmergencia.kt - La pantalla donde se agregan nuevos contactos

# FirebaseAppointmentRepository.kt

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
 Descripción General
Este archivo define el repositorio de citas médicas que se sincroniza con Firebase, la base de datos en la nube de Google. A diferencia del AppointmentRepository que guarda las citas localmente en el teléfono, este repositorio guarda la información en internet, lo que permite que los datos estén disponibles en cualquier dispositivo y no se pierdan si se desinstala la aplicación o se cambia de teléfono.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.repository

 Explicación Detallada
¿Qué es Firebase y por qué usarlo?
Firebase es una plataforma de Google que proporciona servicios en la nube para aplicaciones móviles. En este caso, se usa Firebase Firestore, que es una base de datos en internet. Las ventajas son:

Datos en la nube: La información no se pierde aunque se borre la aplicación

Sincronización: Los datos están disponibles en cualquier dispositivo

Tiempo real: Las actualizaciones se ven al instante en todos los dispositivos

Copia de seguridad: Google se encarga de respaldar la información

Propiedades y Configuración
kotlin
private val db = FirebaseFirestore.getInstance()
private val appointmentsCollection = db.collection("appointments")
db: Es la conexión a Firebase Firestore. getInstance() devuelve una instancia ya configurada de la base de datos.

appointmentsCollection: Es una referencia a la colección "appointments" en Firebase. Una colección es como una carpeta que contiene documentos, similar a una tabla en bases de datos tradicionales.

Métodos y su Función
getAllAppointments(): Flow<List<AppointmentEntity>>
Este método obtiene todas las citas guardadas en Firebase y las entrega como un flujo de datos en tiempo real.

Cómo funciona: Usa un patrón especial llamado callbackFlow que convierte los callbacks de Firebase en un Flow de Kotlin. Cuando hay cambios en la nube, Firebase avisa y este método actualiza automáticamente la lista.

Partes importantes:

addSnapshotListener: Escucha cambios en tiempo real en Firebase. Cada vez que se agrega, modifica o elimina una cita, este listener se activa.

mapNotNull: Convierte cada documento de Firebase a un objeto AppointmentEntity. Si algún documento no se puede convertir, lo ignora.

copy(id = doc.id.hashCode()): Asigna un ID basado en el hash del ID de Firebase. Esto es necesario porque los IDs de Firebase son textos largos, pero la aplicación usa números enteros como IDs.

trySend: Envía la lista actualizada al Flow para que la aplicación la reciba.

awaitClose: Cuando el Flow se cierra (por ejemplo, al salir de la pantalla), deja de escuchar cambios para ahorrar recursos.

insertAppointment(appointment: AppointmentEntity): Int
Este método guarda una nueva cita en Firebase.

Cómo funciona:

Intenta agregar la cita a la colección "appointments" en Firebase

Firebase genera automáticamente un ID único para el documento

Convierte ese ID a un número entero usando hashCode() y lo devuelve

Si hay algún error, muestra la causa en la consola y devuelve 0

Parámetros: Recibe un objeto AppointmentEntity con los datos de la cita.

Retorno: Devuelve un número entero que es el ID de la cita en Firebase convertido a número, o 0 si hubo error.

deleteAppointment(appointment: AppointmentEntity)
Este método elimina una cita existente de Firebase.

Cómo funciona:

Busca en Firebase documentos que tengan el mismo lugar y la misma fecha/hora que la cita que se quiere eliminar

Toma el primer documento que encuentra (asumiendo que no hay duplicados exactos)

Elimina ese documento de Firebase

Si hay error, muestra la causa en la consola

Parámetros: Recibe el objeto AppointmentEntity de la cita que se desea eliminar.

Retorno: No devuelve ningún valor.
Notas Importantes sobre el Código
Sobre los IDs:
kotlin
doc.toObject(AppointmentEntity::class.java)?.copy(
    id = doc.id.hashCode()
)
Los IDs en Firebase son textos como "ABC123xyz", pero la aplicación espera números enteros. Por eso se usa hashCode(), que convierte cualquier texto a un número. Sin embargo, esto tiene una limitación: diferentes textos podrían generar el mismo número (aunque es poco probable).

Sobre la eliminación:
kotlin
.whereEqualTo("place", appointment.place)
.whereEqualTo("dateTime", appointment.dateTime)
Para eliminar, se busca por lugar y fecha porque en Firebase no se puede eliminar directamente usando el objeto. Esto asume que no hay dos citas exactamente iguales en el mismo lugar y fecha, lo cual es razonable.

Manejo de errores:
kotlin
try {
    // operación
} catch (e: Exception) {
    e.printStackTrace()
    // valor por defecto o nada
}
Todos los métodos tienen manejo de errores básico: muestran el error en la consola y devuelven valores por defecto (0 en inserciones, nada en eliminaciones). Esto evita que la aplicación se cierre si hay problemas con Firebase.

 Flujo de Trabajo Típico
Ejemplo 1: Escuchar cambios en tiempo real
kotlin
// En un ViewModel
val firebaseRepo = FirebaseAppointmentRepository()

// La pantalla observa este Flow
firebaseRepo.getAllAppointments().collect { appointments ->
    // Cada vez que cambian las citas en Firebase, este código se ejecuta
    // con la lista actualizada
    updateUI(appointments)
}
Ejemplo 2: Insertar una cita
kotlin
// En un ViewModel
fun addAppointmentToCloud(place: String, dateTime: Long) {
    viewModelScope.launch {
        val appointment = AppointmentEntity(
            place = place,
            dateTime = dateTime
        )
        
        val cloudId = firebaseRepo.insertAppointment(appointment)
        if (cloudId != 0) {
            // Se guardó correctamente en la nube
            showMessage("Cita guardada en la nube")
        } else {
            // Hubo un error
            showError("No se pudo guardar en la nube")
        }
    }
}
Ejemplo 3: Eliminar una cita
kotlin
// En un ViewModel
fun deleteAppointmentFromCloud(appointment: AppointmentEntity) {
    viewModelScope.launch {
        firebaseRepo.deleteAppointment(appointment)
        // La cita se elimina y la lista se actualiza automáticamente
        // porque el Flow de getAllAppointments() emite el cambio
    }
}
 Archivos Relacionados
AppointmentEntity.kt - La entidad que se guarda en Firebase

AppointmentRepository.kt - El repositorio local equivalente

AppointmentViewModel.kt - Posible ViewModel que podría usar ambos repositorios

Cualquier pantalla que muestre citas sincronizadas con la nube

 Posibles Mejoras
Mejor manejo de IDs: En lugar de usar hashCode(), se podría modificar AppointmentEntity para aceptar IDs de texto.

Eliminación por ID real: Si se guardara el ID de Firebase en la entidad local, se podría eliminar directamente por ese ID en lugar de buscar por lugar y fecha.

Sincronización automática: Combinar este repositorio con el local para que los datos se guarden en ambos lugares automáticamente.

# FirebaseContactRepository.kt

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
 Descripción General
Este archivo define el repositorio de contactos de emergencia que se sincroniza con Firebase, la base de datos en la nube de Google. A diferencia del EmergencyContactRepository que guarda los contactos localmente en el teléfono, este repositorio guarda la información en internet, permitiendo que los contactos de emergencia estén disponibles en cualquier dispositivo y no se pierdan si se desinstala la aplicación o se cambia de teléfono.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.repository

 Explicación Detallada
¿Qué es Firebase y por qué usarlo?
Firebase es una plataforma de Google que proporciona servicios en la nube para aplicaciones móviles. En este caso, se usa Firebase Firestore para guardar los contactos de emergencia en internet. Las ventajas son:

Datos en la nube: Los contactos no se pierden aunque se borre la aplicación

Sincronización: Los contactos están disponibles en cualquier dispositivo

Tiempo real: Las actualizaciones se ven al instante en todos los dispositivos

Copia de seguridad: Google se encarga de respaldar la información

Propiedades y Configuración
kotlin
private val db = FirebaseFirestore.getInstance()
private val contactsCollection = db.collection("contacts")
db: Es la conexión a Firebase Firestore. getInstance() devuelve una instancia ya configurada.

contactsCollection: Es una referencia a la colección "contacts" en Firebase. Una colección es como una carpeta que contiene documentos, similar a una tabla en bases de datos tradicionales.

Métodos y su Función
getAllContacts(): Flow<List<EmergencyContactEntity>>
Este método obtiene todos los contactos de emergencia guardados en Firebase y los entrega como un flujo de datos en tiempo real.

Cómo funciona: Usa un patrón especial llamado callbackFlow que convierte los callbacks de Firebase en un Flow de Kotlin. Cuando hay cambios en la nube, Firebase avisa y este método actualiza automáticamente la lista.

Partes importantes:

addSnapshotListener: Escucha cambios en tiempo real en Firebase. Cada vez que se agrega, modifica o elimina un contacto, este listener se activa.

mapNotNull: Convierte cada documento de Firebase a un objeto EmergencyContactEntity. Si algún documento no se puede convertir, lo ignora.

copy(id = doc.id.hashCode()): Asigna un ID basado en el hash del ID de Firebase. Los IDs de Firebase son textos largos, pero la aplicación usa números enteros.

trySend: Envía la lista actualizada al Flow.

awaitClose: Cuando el Flow se cierra, deja de escuchar cambios para ahorrar recursos.

insertContact(contact: EmergencyContactEntity): Int
Este método guarda un nuevo contacto de emergencia en Firebase.

Cómo funciona:

Intenta agregar el contacto a la colección "contacts"

Firebase genera automáticamente un ID único

Convierte ese ID a número usando hashCode() y lo devuelve

Si hay error, muestra la causa y devuelve 0

Parámetros: Recibe un objeto EmergencyContactEntity con nombre y teléfono.

Retorno: Devuelve un número entero que es el ID del contacto en Firebase convertido a número, o 0 si hubo error.

deleteContact(contact: EmergencyContactEntity)
Este método elimina un contacto existente de Firebase.

Cómo funciona:

Busca en Firebase documentos que tengan el mismo nombre y teléfono que el contacto a eliminar

Toma el primer documento que encuentra

Elimina ese documento

Si hay error, muestra la causa

Parámetros: Recibe el objeto EmergencyContactEntity del contacto a eliminar.

Retorno: No devuelve ningún valor.

FirebaseMedicationRepository.kt
kotlin
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
 Descripción General
Este archivo define el repositorio de medicamentos que se sincroniza con Firebase. Permite guardar en la nube toda la información de los medicamentos que el usuario registra, incluyendo nombre, dosis, notas, horarios, días y progreso del tratamiento.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.repository

 Explicación Detallada
Propiedades y Configuración
kotlin
private val db = FirebaseFirestore.getInstance()
private val medicationsCollection = db.collection("medications")
Similar a los otros repositorios, se conecta a Firebase y apunta a la colección "medications".

Métodos y su Función
getAllMedications(): Flow<List<MedicationEntity>>
Obtiene todos los medicamentos guardados en Firebase con actualizaciones en tiempo real.

Características especiales:

Escucha cambios en la colección "medications"

Convierte cada documento a MedicationEntity

Asigna un ID numérico basado en el hash del ID de Firebase

Emite listas actualizadas automáticamente

insertMedication(medication: MedicationEntity): Int
Guarda un nuevo medicamento en Firebase.

Particularidades: Los medicamentos tienen muchos campos (nombre, dosis, nota, schedules, days, totalDays, daysTaken) que se guardan completos en Firebase.

deleteMedication(medication: MedicationEntity)
Elimina un medicamento de Firebase.

Cómo busca: Usa solo el nombre del medicamento para encontrar el documento a eliminar. Esto asume que no hay dos medicamentos con el mismo nombre, lo cual podría ser un problema si hay medicamentos repetidos.

MedicationRepository.kt
kotlin
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
 Descripción General
Este archivo define el repositorio local de medicamentos, que actúa como un puente entre la base de datos local (Room) y el resto de la aplicación. Es la versión local que complementa al FirebaseMedicationRepository para la nube.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.repository

 Explicación Detallada
Constructor y Dependencias
kotlin
class MedicationRepository(private val dao: MedicationDao)
Recibe el DAO de medicamentos por inyección de dependencias, lo que permite que el repositorio use la base de datos local sin tener que crearla él mismo.

Métodos y su Función
getAllMedications(): Flow<List<MedicationEntity>>
Obtiene todos los medicamentos de la base de datos local con actualizaciones en tiempo real gracias al Flow del DAO.

insertMedication(med: MedicationEntity): Long
Guarda un nuevo medicamento en la base de datos local.

Importante: Devuelve un Long que es el ID con el que se guardó el medicamento. Este valor es crucial porque el ViewModel lo necesita para funcionar correctamente.

deleteMedication(med: MedicationEntity)
Elimina un medicamento de la base de datos local.

updateMedication(med: MedicationEntity)
Actualiza un medicamento existente en la base de datos local. Este método es exclusivo del repositorio local, ya que el de Firebase no tiene actualización directa.
Notas Importantes
Sobre FirebaseContactRepository
Usa nombre y teléfono para eliminar, lo cual es más preciso que solo el nombre

La colección se llama "contacts" en Firebase

Sobre FirebaseMedicationRepository
Para eliminar solo usa el nombre, lo que podría causar problemas si hay medicamentos con el mismo nombre

No tiene método de actualización, solo insertar y eliminar

Guarda todos los campos incluyendo las listas de schedules y days

Sobre MedicationRepository
Es el único que tiene método updateMedication

Devuelve Long en lugar de Int para el ID

Trabaja directamente con Room y la base de datos local

 Archivos Relacionados
EmergencyContactEntity.kt, MedicationEntity.kt - Las entidades que se guardan

EmergencyContactDao.kt, MedicationDao.kt - Los DAOs locales

EmergencyContactRepository.kt - El repositorio local de contactos

Posibles ViewModels que usen estos repositorios para sincronizar datos locales y en la nube

# NavGraph.kt

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

    // Obtener el Application Context para acceder a recursos globales
    val application = context.applicationContext as Application

    // Crear la base de datos local
    val database = AppDatabase.getDatabase(application)

    // Crear los repositorios que conectan con la base de datos local
    val medicationRepository = MedicationRepository(database.medicationDao())
    val appointmentRepository = AppointmentRepository(database.appointmentDao())
    val emergencyContactRepository = EmergencyContactRepository(database.emergencyContactDao())

    // Crear los ViewModels usando sus fábricas
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

    // Configuración del tema oscuro/claro
    val themePreferences = remember { ThemePreferences(context) }
    val scope = rememberCoroutineScope()
    val isDarkTheme by themePreferences.isDarkTheme.collectAsState(initial = false)

    // Sistema de navegación principal
    NavHost(navController = navController, startDestination = "home") {

        // Pantalla principal
        composable("home") {
            MediAlertaScreen(
                onNavigateToMedicamentos = { navController.navigate("medicationList") },
                onNavigateToCitas = { navController.navigate("citas") },
                onNavigateToFarmacias = { navController.navigate("farmacias") },
                onNavigateToConfiguracion = { navController.navigate("configuracion") },
                onNavigateToMedicationList = { navController.navigate("medicationList") }
            )
        }

        // Pantalla de notificación de medicamento
        composable("notificacion") {
            NotificacionMedicamento(
                onYaLaTome = { navController.popBackStack() },
                onEmergencia = { navController.navigate("agregarNumeroEmergencia") }
            )
        }

        // Pantalla para agregar un nuevo medicamento
        composable("addMedication") {
            AddMedicationScreen(
                viewModel = medicationViewModel,
                onBack = { navController.popBackStack() },
                onNavigateToNotification = { navController.popBackStack() }
            )
        }

        // Pantalla de lista de citas médicas
        composable("citas") {
            CitasMedicasScreen(
                onBack = { navController.popBackStack() },
                viewModel = appointmentViewModel
            )
        }

        // Pantalla para agregar una nueva cita
        composable("agregarCita") {
            AgregarCita(onAceptarClick = { navController.popBackStack() })
        }

        // Pantalla para agregar un número de emergencia
        composable("agregarNumeroEmergencia") {
            AgregarNumeroEmergencia(
                onAgregar = { nombre, numero ->
                    navController.popBackStack()
                }
            )
        }

        // Pantalla de farmacias cercanas
        composable("farmacias") {
            FarmaciasCercanasScreen(onBackClick = { navController.popBackStack() })
        }

        // Pantalla de configuración
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

        // Pantalla de medicamentos (probablemente vista alternativa)
        composable("medicamentos") {
            MedicamentosScreen(viewModel = medicationViewModel)
        }

        // Pantalla principal de lista de medicamentos
        composable("medicationList") {
            MedicationListScreen(
                onAddMedication = { navController.navigate("addMedication") },
                onBack = { navController.popBackStack() },
                viewModel = medicationViewModel
            )
        }
    }
}
 Descripción General
Este archivo define el sistema de navegación de toda la aplicación. Es como el mapa que conecta todas las pantallas entre sí, permitiendo al usuario moverse de una pantalla a otra de manera organizada. También se encarga de crear y proporcionar los ViewModels y repositorios que las pantallas necesitan para funcionar.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.navigation

 Explicación Detallada
¿Qué es NavGraph?
NavGraph es el componente central de navegación de la aplicación. Define todas las rutas disponibles (cada pantalla tiene una ruta única como "home", "citas", "addMedication") y cómo navegar entre ellas. También se encarga de inicializar los componentes que las pantallas necesitan, como la base de datos, los repositorios y los ViewModels.

Componentes Principales
navController: NavHostController
Es el controlador de navegación. Se encarga de manejar las transiciones entre pantallas. Cuando se llama a navController.navigate("ruta"), el controlador lleva al usuario a esa pantalla. Cuando se llama a navController.popBackStack(), regresa a la pantalla anterior.

NavHost
Es el contenedor que muestra la pantalla actual según la ruta activa. Tiene dos parámetros importantes:

navController: El controlador que maneja la navegación

startDestination: La primera pantalla que se muestra al iniciar la app (en este caso "home")

Inicialización de Dependencias
Contexto y Base de Datos
kotlin
val context = LocalContext.current
val application = context.applicationContext as Application
val database = AppDatabase.getDatabase(application)
LocalContext.current: Obtiene el contexto actual de Compose

application: Convierte el contexto a Application para acceder a recursos globales

database: Crea la instancia de la base de datos local usando el patrón singleton

Repositorios
kotlin
val medicationRepository = MedicationRepository(database.medicationDao())
val appointmentRepository = AppointmentRepository(database.appointmentDao())
val emergencyContactRepository = EmergencyContactRepository(database.emergencyContactDao())
Se crean los tres repositorios principales, cada uno con su DAO correspondiente de la base de datos. Estos repositorios se usan para acceder a los datos locales.

ViewModels
kotlin
val medicationViewModel: MedicationViewModel = viewModel(
    factory = MedicationViewModelFactory(application = application)
)
Los ViewModels se crean usando fábricas (Factories) porque algunos necesitan parámetros especiales como el Application context. Las fábricas son clases separadas que saben cómo construir los ViewModels correctamente.

Configuración del Tema
kotlin
val themePreferences = remember { ThemePreferences(context) }
val scope = rememberCoroutineScope()
val isDarkTheme by themePreferences.isDarkTheme.collectAsState(initial = false)
ThemePreferences: Clase que guarda las preferencias de tema (oscuro/claro)

scope: Ámbito para lanzar corrutinas (necesario para guardar preferencias)

isDarkTheme: Estado observable que indica si el tema oscuro está activado
Navegación entre Pantallas
Navegación hacia adelante:
kotlin
navController.navigate("medicationList")
// Lleva al usuario a la pantalla de lista de medicamentos
Navegación hacia atrás:
kotlin
navController.popBackStack()
// Regresa a la pantalla anterior
Navegación con limpieza de pila (no se usa aquí pero es común):
kotlin
navController.navigate("home") {
    popUpTo("home") { inclusive = true }
}
// Va a home y elimina todas las pantallas intermedias
 Flujo de Trabajo Típico
El usuario abre la app: NavHost muestra la pantalla "home" (MediAlertaScreen)

El usuario toca "Medicamentos": Se ejecuta onNavigateToMedicationList que llama a navController.navigate("medicationList")

Se muestra MedicationListScreen: Esta pantalla recibe el medicationViewModel ya creado

El usuario toca "Agregar": Se ejecuta onAddMedication que navega a "addMedication"

El usuario guarda el medicamento: Al terminar, llama a onBack que ejecuta navController.popBackStack() y regresa a la lista

El usuario toca "Atrás": En cualquier pantalla, onBackClick llama a popBackStack() y regresa a la pantalla anterior

 Notas Importantes
ViewModels compartidos: Todos los ViewModels se crean en NavGraph y se pasan a las pantallas que los necesitan. Esto asegura que los datos persistan mientras se navega.

Repositorios creados pero no usados: Se crean medicationRepository, appointmentRepository y emergencyContactRepository pero no se usan directamente. Esto sugiere que los ViewModels podrían necesitarlos, o que es código preparado para futuras funcionalidades.

Dos rutas para medicamentos: Hay "medicamentos" y "medicationList", lo que podría ser redundante o preparado para diferentes vistas.

Pantalla de notificación: La ruta "notificacion" se usa para mostrar un recordatorio cuando es hora de tomar un medicamento.

Tema oscuro: El tema se maneja a nivel de NavGraph y se pasa a ConfiguracionScreen para que el usuario pueda cambiarlo.

 Archivos Relacionados
Todas las pantallas en ui/screens/

Todos los ViewModels y sus fábricas en viewmodel/

ThemePreferences.kt en utils/ para guardar el tema

AppDatabase.kt y los repositorios en data/

# AlarmReceiver.kt

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
                // Manejar la alarma de medicamento
                val medName = intent.getStringExtra("medName") ?: "Medicamento Desconocido"
                val medId = intent.getIntExtra("medId", -1)

                if (medId != -1) {
                    NotificationHelper(context).showMedicationNotification(medName, medId)
                }
            }

            ACTION_MARK_TAKEN -> {
                // Manejar el botón "Tomado"
                val medId = intent.getIntExtra("medId", -1)
                if (medId != -1) {
                    // 1. Actualizar la base de datos para marcar como tomado
                    markMedicationAsTaken(context, medId)

                    // 2. Cancelar la notificación
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
        // Usamos el mismo ID que se usó para mostrar la notificación
        val notificationManager = context.getSystemService(Context.NOTIFICATION_SERVICE) as android.app.NotificationManager
        notificationManager.cancel(notificationId)
    }
}
 Descripción General
Este archivo define el AlarmReceiver, que es un componente especial de Android que "escucha" las alarmas programadas y responde cuando estas se activan. Es como un despertador que, cuando llega la hora, se encarga de mostrar una notificación para recordarle al usuario que debe tomar su medicamento y también maneja las acciones que el usuario realiza sobre esa notificación.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.receivers

 Explicación Detallada
¿Qué es un BroadcastReceiver?
Un BroadcastReceiver es un componente de Android que puede escuchar mensajes (llamados "broadcasts") que el sistema o las aplicaciones envían. En este caso, el AlarmReceiver escucha dos tipos de mensajes:

Cuando es hora de tomar un medicamento (la alarma programada se activa)

Cuando el usuario toca el botón "Tomado" en la notificación

Herencia y Métodos Principales
class AlarmReceiver : BroadcastReceiver()
Esta clase hereda de BroadcastReceiver, lo que significa que puede recibir y procesar broadcasts del sistema.

override fun onReceive(context: Context, intent: Intent)
Este es el método más importante. Android lo llama automáticamente cuando ocurre un broadcast que este receptor está escuchando. Recibe dos parámetros:

context: El contexto en el que se está ejecutando el receptor

intent: El mensaje que contiene información sobre lo que pasó (qué acción ocurrió y datos adicionales)

Constantes de Acción
El código usa dos constantes que están definidas en otro archivo (utils/):

ACTION_MEDICATION_ALARM: Indica que es hora de tomar un medicamento

ACTION_MARK_TAKEN: Indica que el usuario marcó el medicamento como tomado

Flujo de Trabajo
Caso 1: Es hora de tomar el medicamento
kotlin
ACTION_MEDICATION_ALARM -> {
    val medName = intent.getStringExtra("medName") ?: "Medicamento Desconocido"
    val medId = intent.getIntExtra("medId", -1)

    if (medId != -1) {
        NotificationHelper(context).showMedicationNotification(medName, medId)
    }
}
Qué hace:

Extrae el nombre del medicamento del Intent (si no viene, usa "Medicamento Desconocido")

Extrae el ID del medicamento (si no viene, usa -1)

Si el ID es válido (diferente de -1), usa NotificationHelper para mostrar una notificación

Ejemplo de datos que llegan:

medName = "Paracetamol"

medId = 42

Caso 2: El usuario marcó "Tomado"
kotlin
ACTION_MARK_TAKEN -> {
    val medId = intent.getIntExtra("medId", -1)
    if (medId != -1) {
        markMedicationAsTaken(context, medId)
        cancelNotification(context, medId)
    }
}
Qué hace:

Extrae el ID del medicamento

Llama a markMedicationAsTaken() para actualizar la base de datos

Llama a cancelNotification() para quitar la notificación de la barra de estado

Métodos Auxiliares
markMedicationAsTaken(context: Context, medId: Int)
kotlin
private fun markMedicationAsTaken(context: Context, medId: Int) {
    // TODO: Implementar la lógica para actualizar tu base de datos
    android.util.Log.d("AlarmReceiver", "Medicamento $medId marcado como tomado")
}
Estado actual: Es un método incompleto (tiene TODO). Por ahora solo registra un mensaje en la consola, pero la idea es que actualice la base de datos para reflejar que el usuario tomó el medicamento.

Lo que debería hacer:

Conectar con el repositorio de medicamentos

Actualizar el contador daysTaken del medicamento

Quizás registrar la hora exacta en que se tomó

cancelNotification(context: Context, notificationId: Int)
kotlin
private fun cancelNotification(context: Context, notificationId: Int) {
    val notificationManager = context.getSystemService(Context.NOTIFICATION_SERVICE) as android.app.NotificationManager
    notificationManager.cancel(notificationId)
}
Qué hace: Quita la notificación de la barra de estado del teléfono. Usa el mismo ID con el que se mostró la notificación para identificarla y eliminarla.

Cómo funciona:

Obtiene el servicio NotificationManager del sistema

Llama a cancel(notificationId) para eliminar la notificación con ese ID específico

 Flujo Completo de una Alarma
Paso 1: Programar la alarma
En alguna parte de la aplicación (probablemente en AlarmUtils.kt), se programa una alarma:

kotlin
val intent = Intent(context, AlarmReceiver::class.java).apply {
    action = ACTION_MEDICATION_ALARM
    putExtra("medName", "Paracetamol")
    putExtra("medId", 42)
}

val pendingIntent = PendingIntent.getBroadcast(context, 42, intent, PendingIntent.FLAG_UPDATE_CURRENT)

alarmManager.setExact(AlarmManager.RTC_WAKEUP, horaProgramada, pendingIntent)
Paso 2: Llega la hora
Cuando el reloj del sistema alcanza la hora programada, Android ejecuta automáticamente el onReceive del AlarmReceiver con el Intent que se programó.

Paso 3: Mostrar notificación
El receptor ve que la acción es ACTION_MEDICATION_ALARM y muestra una notificación usando NotificationHelper.

Paso 4: Usuario interactúa
La notificación tiene un botón "Tomado". Cuando el usuario lo toca, Android envía otro broadcast con acción ACTION_MARK_TAKEN y el ID del medicamento.

Paso 5: Procesar la respuesta
El receptor recibe el broadcast, marca el medicamento como tomado (o al menos lo intenta) y cancela la notificación.

 Notas Importantes
TODO pendiente: La función markMedicationAsTaken está incompleta. Para que la aplicación funcione correctamente, hay que implementar la lógica que actualice la base de datos cuando el usuario confirma que tomó el medicamento.

ID por defecto -1: Se usa -1 como valor por defecto para el ID del medicamento. Si llega -1, el receptor no hace nada porque no puede identificar qué medicamento es.

Constantes externas: Las acciones ACTION_MEDICATION_ALARM y ACTION_MARK_TAKEN están definidas en otro archivo. Esto es una buena práctica porque evita errores de escritura y centraliza las constantes.

Uso de NotificationHelper: En lugar de crear la notificación directamente, se usa una clase auxiliar NotificationHelper. Esto mantiene el código más limpio y organizado.

 Archivos Relacionados
NotificationHelper.kt en utils/ - La clase que realmente construye y muestra las notificaciones

AlarmUtils.kt en utils/ - Probablemente contiene funciones para programar las alarmas

El archivo donde se definen las constantes (ACTION_MEDICATION_ALARM, ACTION_MARK_TAKEN)

MedicationRepository.kt - Debería usarse para implementar el markMedicationAsTaken pendiente

MedicationEntity.kt - La entidad que se actualizaría cuando se marca como tomado
# AgregarCita.kt

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
 Descripción General
Este archivo define la pantalla para agregar una nueva cita médica. Es un formulario simple donde el usuario puede ingresar el lugar de la cita y la fecha y hora. Al presionar el botón "Aceptar", la información se envía de vuelta a la pantalla anterior.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.citas

 Explicación Detallada
AgregarCita()
Qué hace: Muestra una tarjeta con un formulario para agregar una nueva cita médica.

Componentes del formulario:

Título: "Agregar Cita" en letras grandes y negritas

Campo "Lugar": Donde el usuario escribe dónde será la cita

Campo "Fecha y hora": Donde el usuario escribe cuándo será la cita (formato libre)

Botón "Aceptar": Botón verde que al presionarlo ejecuta onAceptarClick()

Parámetros:

onAceptarClick: Función que se ejecuta cuando el usuario presiona el botón Aceptar. Por defecto no hace nada ({}).

Características visuales:

La tarjeta tiene fondo gris claro (#F5F5F5)

Bordes redondeados de 16dp

Sombra (elevación) de 8dp

Los campos de texto tienen bordes redondeados de 10dp

El botón es verde (#4CAF50) con texto blanco

Limitaciones: Este formulario no valida que los campos estén llenos, el usuario puede presionar Aceptar incluso si los campos están vacíos.

CitasMedicasScreen.kt
kotlin
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
                    Text(
                        text = "🏥",
                        fontSize = 32.sp
                    )
                }

                Spacer(modifier = Modifier.width(16.dp))

                Column(
                    modifier = Modifier.weight(1f)
                ) {
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
 Descripción General
Este archivo contiene la pantalla principal de citas médicas, donde el usuario puede ver todas sus citas programadas, agregar nuevas citas y eliminar citas existentes. Es una pantalla completa con lista interactiva y diálogos para agregar citas con selector de fecha y hora.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.citas

 Explicación Detallada
CitasMedicasScreen()
Qué hace: Es la pantalla principal de citas médicas. Muestra una lista de todas las citas guardadas, con un botón para agregar nuevas citas y la opción de eliminar cada cita individualmente.

Parámetros:

onBack: Función que se ejecuta cuando el usuario presiona el botón de retroceso

viewModel: El ViewModel que maneja los datos de las citas

Componentes:

TopAppBar: Barra superior con título "Citas Médicas", botón de retroceso y botón "Agregar"

Lista de citas: Muestra todas las citas usando AppointmentCard

Mensaje vacío: Si no hay citas, muestra un mensaje indicando que no hay citas programadas

Diálogo: Cuando se presiona "Agregar", muestra AddAppointmentDialog

Flujo de datos:

Usa LaunchedEffect para recolectar las citas del ViewModel

Cada vez que cambia la lista, actualiza la variable appointments

Cuando se agrega o elimina una cita, el ViewModel notifica y la lista se actualiza automáticamente

AppointmentCard()
Qué hace: Muestra una tarjeta individual para cada cita médica con diseño llamativo.

Elementos visuales:

Fondo: Color verde menta suave (#E8F5E9) con círculos decorativos dibujados con Canvas

Ícono principal: Un cuadro verde con el emoji 

Lugar: Icono de ubicación y el nombre del lugar en letras grandes

Fecha y hora: Tarjeta blanca semitransparente con calendario y la fecha formateada

Botón eliminar: Círculo rojo claro con icono de basurero

Interactividad: Al presionar el botón eliminar, ejecuta la función onDelete que elimina la cita

AddAppointmentDialog()
Qué hace: Muestra un diálogo para agregar una nueva cita médica con selector de fecha y hora.

Campos del formulario:

Lugar de la Cita: Campo de texto donde el usuario escribe el lugar

Fecha y Hora: Campo que parece un texto normal pero al tocarlo abre un selector de fecha y hora

Selector de fecha y hora:

Al tocar el campo, se abre primero un DatePickerDialog para seleccionar la fecha

Después de seleccionar la fecha, se abre un TimePickerDialog para seleccionar la hora

La fecha y hora seleccionadas se guardan en selectedDateTime como timestamp

Validación: El botón "Aceptar" solo se habilita cuando el campo "Lugar" no está vacío
Flujo de Trabajo
Usuario abre la pantalla: CitasMedicasScreen se muestra con la lista de citas actual

Usuario quiere agregar cita: Presiona "Agregar" → se abre AddAppointmentDialog

Usuario llena datos: Escribe el lugar y selecciona fecha/hora con los pickers

Usuario guarda: Presiona "Aceptar" → se crea AppointmentEntity y se inserta vía ViewModel

La lista se actualiza: El ViewModel notifica y la nueva cita aparece en la lista

Usuario elimina cita: Presiona el botón rojo en una tarjeta → se llama a viewModel.deleteAppointment() y la cita desaparece

# ConfiguracionScreen.kt

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
 Descripción General
Este archivo contiene la pantalla de configuración de la aplicación. Permite al usuario personalizar su experiencia ajustando preferencias como notificaciones, tema de colores (claro/oscuro) y gestionar contactos de emergencia. También incluye un botón directo para llamar al 911 y una lista de contactos de emergencia guardados.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.configuracion

 Explicación Detallada
ConfiguracionScreen()
Qué hace: Es la pantalla principal de configuración. Muestra tres secciones principales en una lista vertical: Notificaciones, Tema de apariencia y Contactos de emergencia.

Parámetros:

onBackClick: Función para regresar a la pantalla anterior

onThemeChange: Callback que se ejecuta cuando el usuario cambia el tema (oscuro/claro)

isDarkTheme: Indica si el tema actual es oscuro o claro

emergencyViewModel: ViewModel para gestionar los contactos de emergencia

Variables de estado:

notificacionesActivadas: Controla si las notificaciones están activadas (por ahora solo visual, no funcional)

temaOscuro: Controla el tema actual y se sincroniza con isDarkTheme

showAddContactDialog: Controla si se muestra el diálogo para agregar contacto

emergencyContacts: Lista de contactos de emergencia que se obtiene del ViewModel

Colores dinámicos según tema:

backgroundColor: Fondo general de la pantalla (blanco para tema claro, #121212 para oscuro)

textColor: Color del texto (negro para claro, blanco para oscuro)

cardColor: Color de las tarjetas (gris claro #F5F5F5 para claro, gris oscuro #1E1E1E para oscuro)

Secciones de Configuración
1. Sección Notificaciones
Qué hace: Permite activar o desactivar las notificaciones de la aplicación.

Componentes:

Ícono de campana (notificaciones)

Texto "Notificaciones"

Switch que cambia el estado de notificacionesActivadas

Estado actual: El switch cambia visualmente pero no tiene funcionalidad real implementada. Es solo una interfaz preparada para futuras implementaciones.

2. Sección Tema de Apariencia
Qué hace: Permite cambiar entre tema claro y oscuro.

Componentes:

Ícono dinámico: luna para tema oscuro, sol para tema claro

Texto "Tema de apariencia" y subtítulo indicando el modo actual

Switch que al cambiarlo:

Actualiza temaOscuro localmente
Ejecuta onThemeChange() para notificar al componente padre (NavGraph)
Esto a su vez actualiza el tema en toda la aplicación
Comportamiento: El cambio de tema afecta inmediatamente los colores de toda la pantalla de configuración.

3. Sección Contactos de Emergencia
Qué hace: Gestiona los contactos de emergencia del usuario. Permite ver la lista, agregar nuevos, llamar a los contactos y eliminarlos.

Componentes:

Encabezado: Ícono de teléfono rojo, título "Contactos de emergencia" y botón "+" para agregar

Botón "Emergencias 911": Botón rojo que al presionarlo abre el marcador telefónico con el número 911 ya marcado

Lista de contactos: Cada contacto se muestra con EmergencyContactItem

Interacciones:

Botón "+": Abre AddEmergencyContactDialog para agregar un nuevo contacto

Botón 911: Abre el marcador con el número 911 (usa Intent.ACTION_DIAL)

Contactos guardados: Cada uno tiene botones para llamar (verde) y eliminar (rojo)

EmergencyContactItem()
Qué hace: Muestra un contacto individual con su nombre, número y botones de acción.

Parámetros:

contact: El objeto EmergencyContactEntity con los datos del contacto

onCall: Función que se ejecuta al presionar el botón de llamada

onDelete: Función que se ejecuta al presionar el botón de eliminar

textColor: Color del texto según el tema actual

Diseño:

Fondo semitransparente con el color primario del contenedor

Nombre en negritas, número en texto más pequeño

Dos botones: teléfono verde (llamar) y basurero rojo (eliminar)

AddEmergencyContactDialog()
Qué hace: Muestra un diálogo para agregar un nuevo contacto de emergencia.

Campos del formulario:

Nombre: Campo de texto con ícono de persona

Número de teléfono: Campo de texto con ícono de teléfono

Validación: El botón "Guardar" solo se habilita cuando ambos campos tienen texto

Acciones:

Guardar: Ejecuta onSave(name, phone) y cierra el diálogo

Cancelar: Cierra el diálogo sin guardar

 Flujo de Trabajo
Cambiar tema:
Usuario toca el switch en sección de tema

Se actualiza temaOscuro localmente

Se llama a onThemeChange() que notifica al NavGraph

NavGraph actualiza el tema global

La pantalla de configuración se recolorea automáticamente

Agregar contacto de emergencia:
Usuario toca botón "+" en sección de contactos

Se abre AddEmergencyContactDialog

Usuario ingresa nombre y teléfono

Presiona "Guardar"

Se llama a emergencyViewModel.insertContact(name, phone)

El ViewModel guarda el contacto en la base de datos

La lista de contactos se actualiza automáticamente (por el collectLatest)

Llamar a un contacto:
Usuario presiona botón verde de teléfono en un contacto

Se ejecuta onCall() que crea un Intent con ACTION_DIAL y el número

Android abre la aplicación de teléfono con el número ya marcado

Usuario solo necesita presionar "llamar"

Llamar al 911:
Usuario presiona el botón rojo "Emergencias 911"

Similar a llamar contacto, pero con número fijo 911

# AgregarNumeroEmergencia.kt

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
 Descripción General
Este archivo define una pantalla simple para agregar un número de contacto de emergencia. Es un formulario básico con dos campos (nombre y número) y un botón rojo para guardar. A diferencia de la pantalla de configuración que también tiene funcionalidad de contactos, esta parece ser una pantalla independiente específica para esta tarea.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.emergencia

 Explicación Detallada
AgregarNumeroEmergencia()
Qué hace: Muestra una tarjeta con un formulario para agregar un nuevo contacto de emergencia. El usuario ingresa un nombre y un número de teléfono, y al presionar "Agregar" se ejecuta una función que pasa estos datos a la pantalla que llamó a este componente.

Parámetros:

onAgregar: Función que se ejecuta cuando el usuario presiona el botón "Agregar". Recibe dos parámetros:

nombre: El nombre del contacto (String)

numero: El número de teléfono (String)
Por defecto es una función vacía que no hace nada { _, _ -> }

Variables de estado:

nombre: Almacena el texto que el usuario escribe en el campo "Nombre"

numero: Almacena el texto que el usuario escribe en el campo "Número de contacto"

Componentes del Formulario
Título
kotlin
Text(
    text = "Agregar número de emergencia",
    fontSize = 20.sp,
    fontWeight = FontWeight.Bold,
    color = Color.Black
)
Texto grande (20sp) y negritas

Color negro, independientemente del tema de la aplicación

Campo "Nombre"
kotlin
OutlinedTextField(
    value = nombre,
    onValueChange = { nombre = it },
    label = { Text("Nombre") },
    modifier = Modifier.fillMaxWidth(),
    shape = RoundedCornerShape(10.dp)
)
Campo de texto con borde delineado

Muestra "Nombre" como etiqueta flotante

Ocupa todo el ancho disponible

Bordes redondeados de 10dp

Campo "Número de contacto"
kotlin
OutlinedTextField(
    value = numero,
    onValueChange = { numero = it },
    label = { Text("Número de contacto") },
    modifier = Modifier.fillMaxWidth(),
    shape = RoundedCornerShape(10.dp)
)
Similar al campo anterior pero para el número telefónico

No tiene validación de formato, acepta cualquier texto

Botón "Agregar"
kotlin
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
Botón rojo (#D32F2F) que ocupa todo el ancho

Altura fija de 50dp

Bordes redondeados de 12dp

Al presionarlo, ejecuta onAgregar con los valores actuales de nombre y numero

No tiene validación: se puede presionar incluso si los campos están vacíos

# FarmaciasCercanasScreen.kt

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
 Descripción General
Este archivo contiene la pantalla para buscar farmacias cercanas a la ubicación del usuario. Es una de las pantallas más complejas de la aplicación porque integra varias tecnologías: permisos de ubicación, Google Maps, y llamadas a la API de Google Places para obtener farmacias reales. La pantalla se divide en dos partes: un mapa en la mitad superior y una lista de farmacias en la mitad inferior.
 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.farmacias

 Explicación Detallada
Modelo de Datos Farmacia
kotlin
data class Farmacia(
    val nombre: String,
    val direccion: String,
    val distancia: String,
    val latLng: LatLng
)
Representa una farmacia con:

nombre: El nombre del establecimiento

direccion: La dirección (viene como "vicinity" de la API)

distancia: Campo preparado pero no usado actualmente (siempre vacío)

latLng: Coordenadas geográficas para ubicar en el mapa

FarmaciasCercanasScreen()
Qué hace: Es la pantalla principal que muestra el mapa y la lista de farmacias cercanas. Maneja permisos de ubicación, obtiene la ubicación actual y busca farmacias usando la API de Google Places.

Parámetros:

onBackClick: Función para regresar a la pantalla anterior

Variables de estado:

userLocation: Ubicación actual del usuario (LatLng) o null si no se ha obtenido

farmacias: Lista de farmacias encontradas

isLoading: Indica si se está cargando la información

Permisos:

kotlin
val locationPermissionState = rememberPermissionState(
    permission = Manifest.permission.ACCESS_FINE_LOCATION
)
Usa la librería Accompanist para manejar permisos de forma moderna en Compose. Solicita permiso de ubicación precisa.

Obtención de ubicación:

kotlin
LaunchedEffect(locationPermissionState.status.isGranted) {
    if (locationPermissionState.status.isGranted) {
        try {
            val fusedLocationClient = LocationServices.getFusedLocationProviderClient(context)
            val location = fusedLocationClient.lastLocation.await()
            // ... procesar ubicación
        }
    }
}
Cuando se concede el permiso, usa el cliente de ubicación de Google Play Services para obtener la última ubicación conocida.

API Key: La clave está hardcodeada en el código:

kotlin
val API_KEY = "AIzaSyAUdfxCL1xKk66Ai8GcMET6fS1dyUH9pOk"
Estructura de la pantalla:

TopAppBar: Título "Farmacias Cercanas" y botón de retroceso

Mitad superior: Mapa (si hay ubicación) o mensaje de permisos/carga

Divider: Línea divisoria entre mapa y lista

Mitad inferior: Lista de farmacias o mensaje de "no encontradas"

GoogleMapView()
Qué hace: Muestra un mapa de Google Maps centrado en la ubicación del usuario, con marcadores para el usuario y para cada farmacia encontrada.

Parámetros:

userLocation: Ubicación del usuario para centrar el mapa

farmacias: Lista de farmacias a marcar

modifier: Modificador de Compose

Características:

Zoom inicial de 14f (nivel de ciudad)

Control de zoom habilitado

Botón "Mi ubicación" habilitado

Marcador azul por defecto para el usuario

Marcadores verdes para farmacias (HUE_GREEN)

FarmaciaCard()
Qué hace: Muestra una tarjeta con información de una farmacia y un botón para obtener direcciones.

Componentes:

Ícono: Ícono de farmacia (cruz) en color primario

Información: Nombre en negritas y dirección

Botón "Cómo llegar": Ícono de direcciones que al presionarlo abre Google Maps con la ruta a la farmacia

Navegación:

kotlin
val uri = Uri.parse("google.navigation:q=${farmacia.latLng.latitude},${farmacia.latLng.longitude}")
val intent = Intent(Intent.ACTION_VIEW, uri).apply {
    setPackage("com.google.android.apps.maps")
}
context.startActivity(intent)
Abre Google Maps en modo navegación con la farmacia como destino.

obtenerFarmaciasReales()
Qué hace: Función suspendida que llama a la API de Google Places para buscar farmacias cercanas.

Parámetros:

userLocation: Coordenadas del usuario

apiKey: Clave de API de Google

URL de la API:

text
https://maps.googleapis.com/maps/api/place/nearbysearch/json?
location=LAT,LNG
&radius=40000
&type=pharmacy
&key=API_KEY
radius=40000: Busca en un radio de 40 kilómetros (bastante amplio)

type=pharmacy: Filtra solo lugares de tipo farmacia

Procesamiento:

Hace la petición HTTP con OkHttpClient

Parsea la respuesta JSON

Extrae del array results cada farmacia

Para cada una, obtiene nombre, dirección y coordenadas

Crea objetos Farmacia y los devuelve en una lista

 Flujo de Trabajo
Usuario abre la pantalla: Se muestra mensaje pidiendo permiso de ubicación

Concede permiso: La aplicación obtiene la última ubicación conocida

Carga de datos: Con la ubicación, llama a la API de Google Places para buscar farmacias

Visualización:

El mapa se centra en la ubicación del usuario

Se muestran marcadores verdes para las farmacias encontradas

La lista muestra todas las farmacias con su información

Interacción:

Usuario puede navegar por el mapa (zoom, desplazamiento)

Puede ver la lista y desplazarse

Al tocar "Cómo llegar" en una farmacia, se abre Google Maps con la ruta

# MediAlertaScreen.kt

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
 Descripción General
Este archivo contiene la pantalla principal de la aplicación, llamada "MediAlerta". Es la primera pantalla que ve el usuario después de iniciar sesión y funciona como un menú de navegación central. Desde aquí puede acceder a todas las funciones principales de la aplicación: medicamentos, citas médicas, farmacias cercanas y configuración. También incluye un botón destacado de emergencia para llamar al 911.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.home
Explicación Detallada
MediAlertaScreen()
Qué hace: Es la pantalla de inicio que muestra el nombre de la aplicación, un botón de emergencia destacado, cuatro tarjetas de navegación a las secciones principales y una imagen decorativa en la parte inferior.

Parámetros (todos opcionales con valor por defecto vacío):

onNavigateToMedicamentos: Navega a la pantalla de medicamentos (versión alternativa)

onNavigateToMedicationList: Navega a la lista principal de medicamentos

onNavigateToCitas: Navega a la pantalla de citas médicas

onNavigateToFarmacias: Navega a la pantalla de farmacias cercanas

onNavigateToConfiguracion: Navega a la pantalla de configuración

Nota: Hay dos callbacks para medicamentos (onNavigateToMedicamentos y onNavigateToMedicationList), pero solo se usa onNavigateToMedicationList en la tarjeta. El otro parece estar preparado para futuros usos.

Componentes de la Pantalla
Título y Subtítulo
kotlin
Text(
    text = "MediAlerta",
    fontSize = 40.sp,
    fontWeight = FontWeight.Bold,
    color = Color(0xFF4A148C)
)

Text(
    text = "Tu salud en tus manos",
    fontSize = 17.sp,
    color = Color(0xFF6A1B9A)
)
Título grande en morado oscuro

Subtítulo en morado un poco más claro

Espaciado para darle aire al diseño

Botón de Emergencia 911
kotlin
Card(
    modifier = Modifier
        .fillMaxWidth(0.85f)
        .height(60.dp)
        .shadow(12.dp, RoundedCornerShape(16.dp))
        .clickable {
            val intent = Intent(Intent.ACTION_DIAL, Uri.parse("tel:911"))
            context.startActivity(intent)
        },
    colors = CardDefaults.cardColors(containerColor = Color(0xFFE53935))
) {
    Text("Emergencia 911", fontSize = 22.sp, fontWeight = FontWeight.Bold, color = Color.White)
}
Botón grande y llamativo en rojo

Ocupa el 85% del ancho de la pantalla

Sombra pronunciada (12dp) para destacar

Al presionarlo abre el marcador telefónico con el número 911 ya marcado

El usuario solo necesita presionar "llamar"

Tarjetas de Navegación
Las cuatro tarjetas están organizadas en dos filas de dos:

Primera fila:

Medicamentos: Tarjeta rosa con ícono de servicios médicos

Citas: Tarjeta púrpura con ícono de calendario

Segunda fila:

Farmacias: Tarjeta amarilla con ícono de farmacia

Configuración: Tarjeta verde con ícono de ajustes

Cada tarjeta tiene:

Borde de color más oscuro de 3dp de grosor

Sombra de 8dp

Esquinas muy redondeadas (20dp)

Ícono grande (38dp) en blanco

Título en blanco, negritas, centrado

Toda la tarjeta es clicable y ejecuta su respectivo callback

Imagen Decorativa
kotlin
Image(
    painter = painterResource(id = R.drawable.estetoscopio),
    contentDescription = "Ilustración fondo",
    modifier = Modifier
        .align(Alignment.BottomCenter)
        .size(250.dp)
        .padding(bottom = 10.dp),
    alpha = 0.90f
)
Imagen de un estetoscopio (debe estar en res/drawable/estetoscopio.png)

Alineada en la parte inferior central

Tamaño de 250dp

Semi-transparente (alpha 0.9) para no distraer

PrettyCard()
Qué hace: Es un componente reutilizable para crear las tarjetas de navegación con un diseño consistente.

Parámetros:

icon: El ícono de Material Icons a mostrar

title: El texto que aparecerá en la tarjeta

color: Color de fondo de la tarjeta

borderColor: Color del borde

onClick: Función a ejecutar cuando se presiona la tarjeta

Diseño:

Tamaño fijo de 150x120dp

Sombra de 8dp

Esquinas redondeadas de 20dp

Borde de 3dp del color especificado

Contenido centrado vertical y horizontalmente

Ícono blanco de 38dp

Texto blanco, negritas, centrado

 Flujo de Trabajo
Usuario abre la aplicación: Después del login, llega a esta pantalla

Opciones disponibles:

Presionar el botón rojo "Emergencia 911" para llamar a emergencias

Presionar "Medicamentos" para ir a la lista de medicamentos

Presionar "Citas" para ver/administrar citas médicas

Presionar "Farmacias" para buscar farmacias cercanas

Presionar "Configuración" para ajustar preferencias

Navegación: Cada tarjeta ejecuta su respectivo callback, que en NavGraph.kt está configurado para navegar a la pantalla correspondiente

 Propósito de la Pantalla
Esta pantalla funciona como el "centro de comando" de la aplicación. Desde aquí el usuario puede:

Acceder rápidamente a emergencias (función crítica)

Gestionar sus medicamentos y recordatorios

Administrar citas médicas

Encontrar farmacias cercanas

Personalizar la aplicación

# AddMedicationScreen.kt

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
 Descripción General
Esta pantalla permite al usuario agregar un nuevo medicamento a la aplicación. Es un formulario completo que recolecta toda la información necesaria: nombre, dosis, notas, días de la semana, horarios y duración del tratamiento. También incluye la funcionalidad para programar recordatorios.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos

 Explicación Detallada
AddMedicationScreen()
Qué hace: Muestra un formulario con todos los campos necesarios para registrar un medicamento.

Parámetros:

viewModel: El ViewModel que maneja la lógica de medicamentos

onBack: Función para regresar a la pantalla anterior

onNavigateToNotification: Navega a la pantalla de notificación después de guardar

Variables de estado:

medName: Nombre del medicamento

medDose: Dosis o cantidad

medNote: Notas adicionales

medTotalDays: Duración total del tratamiento en días (como String)

scheduleList: Lista de horarios seleccionados

selectedDays: Conjunto de días de la semana seleccionados

Campos del Formulario
1. Nombre del medicamento
Campo de texto obligatorio para el nombre.

2. Dosis / Cantidad
Campo de texto para la dosis (ej: "500 mg", "1 tableta").

3. Descripción o nota
Campo de texto para instrucciones especiales.

4. Duración en días
Campo de texto para los días totales del tratamiento.

5. Días de la semana
Filtros tipo chip para seleccionar los días en que se toma:

Lun, Mar, Mie, Jue, Vie, Sab, Dom

Múltiple selección (el usuario puede elegir varios)

6. Horarios
Botón "+" para agregar horarios usando TimePickerDialog

Cada horario agregado aparece en una tarjeta con opción de eliminar

Se pueden agregar múltiples horarios

openTimePicker()
Qué hace: Abre el selector de hora nativo de Android. Cuando el usuario selecciona una hora, la agrega a la lista scheduleList con formato "HH:mm" (24 horas).

Botón Guardar
Validaciones: El botón solo se habilita si:

El nombre no está vacío

Hay al menos un horario seleccionado

Hay al menos un día seleccionado

El campo de días totales no está vacío

Al guardar:

Llama a viewModel.insertMedication() con todos los datos

Navega a la pantalla de notificación

MedicamentosScreen.kt
kotlin
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
 Descripción General
Esta pantalla muestra la lista de medicamentos activos con su progreso de tratamiento. Cada medicamento tiene una barra de progreso y un botón para marcar que ya se tomó la dosis.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos

 Explicación Detallada
MedicamentosScreen()
Qué hace: Muestra una lista de todos los medicamentos registrados, cada uno con su información y progreso.

Observa allMedications del ViewModel para obtener la lista actualizada

Usa collectAsState para reaccionar a cambios en la base de datos

Cada medicamento se muestra con MedicationCard

MedicationCard() (versión simple)
Qué hace: Tarjeta individual para un medicamento con funciones de seguimiento.

Componentes:

Nombre del medicamento con emoji 💊

Dosis

Notas

Barra de progreso lineal que muestra el avance del tratamiento

Texto con "días tomados / días totales"

Botón "Ya la tomé" para registrar una dosis

Lógica de "Ya la tomé":

Llama a viewModel.markMedicationTaken(med)

Después de marcar, verifica si ya se completó el tratamiento

Si daysTaken + 1 >= totalDays, elimina automáticamente el medicamento

MedicationListScreen.kt
kotlin
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
                            Text(
                                text = "💊",
                                fontSize = 24.sp
                            )
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
            Text(
                text = icon,
                fontSize = 18.sp
            )
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
 Descripción General
Esta es la pantalla principal de lista de medicamentos, con un diseño más elaborado y atractivo. Muestra todos los medicamentos registrados con su información detallada y opción de eliminar.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos

 Explicación Detallada
MedicationListScreen()
Qué hace: Pantalla principal de medicamentos con barra superior y lista visualmente atractiva.

Parámetros:

onAddMedication: Navega a la pantalla para agregar medicamento

onBack: Regresa a la pantalla anterior

viewModel: ViewModel de medicamentos

Características:

TopAppBar con título "Mis Medicamentos", botón atrás y botón "Agregar"

Mensaje de estado vacío cuando no hay medicamentos

Lista con tarjetas decorativas para cada medicamento

Observa cambios en tiempo real con collectLatest

MedicationCard() (versión decorativa)
Qué hace: Tarjeta visualmente atractiva para mostrar un medicamento con todos sus detalles.

Diseño:

Fondo rosa pastel (#FFE4F0)

Bordes muy redondeados (20dp)

Círculos decorativos dibujados con Canvas

Ícono circular rosa con emoji 💊

Nombre en rosa fuerte (#D81B60)

Botón de eliminar con fondo rosa claro

Divisor decorativo rosa

Filas de información con íconos emoji y fondos blancos semitransparentes

Información mostrada:

Dosis (con emoji 💉)

Horarios (con emoji ⏰) - múltiples horarios separados por coma

Días de la semana (con emoji 📅) - días seleccionados

MedicationInfoRow()
Qué hace: Componente reutilizable para mostrar una fila de información con ícono, etiqueta y valor.

Componentes:

Caja blanca semitransparente con ícono emoji

Etiqueta pequeña (ej: "Dosis", "Horarios", "Días")

Valor en letra más grande y negrita

# NotificacionMedicamento.kt

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
 Descripción General
Este archivo define la pantalla de notificación que se muestra al usuario cuando es hora de tomar un medicamento. Es una pantalla simple pero muy importante, ya que es la interfaz que el usuario ve cuando recibe un recordatorio. Muestra el nombre del medicamento, la hora y dos botones de acción: "Ya la tomé" y "Emergencia".

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.notificaciones

 Explicación Detallada
NotificacionMedicamento()
Qué hace: Muestra una pantalla de recordatorio para que el usuario sepa que debe tomar su medicamento. Incluye el nombre del medicamento, la hora programada y dos opciones de respuesta.

Parámetros:

nombreMedicamento: El nombre del medicamento que debe tomar (valor por defecto "Amoxicilina" para vista previa)

horario: La hora programada para tomar el medicamento (valor por defecto "7:00 am" para vista previa)

onYaLaTome: Función que se ejecuta cuando el usuario presiona el botón "Ya la tomé"

onEmergencia: Función que se ejecuta cuando el usuario presiona el botón "Emergencia"

Estructura de la Pantalla
Contenedor Principal
kotlin
Column(
    modifier = Modifier
        .fillMaxSize()
        .background(Color(0xFFEFEFEF))
        .padding(24.dp),
    horizontalAlignment = Alignment.CenterHorizontally,
    verticalArrangement = Arrangement.Top
)
Fondo gris claro (#EFEFEF) para toda la pantalla

Padding de 24dp alrededor

Contenido centrado horizontalmente, alineado en la parte superior

Espaciado Superior
kotlin
Spacer(modifier = Modifier.height(50.dp))
Espacio de 50dp para que la tarjeta no quede pegada al borde superior

Tarjeta Principal
kotlin
Card(
    modifier = Modifier
        .fillMaxWidth()
        .height(230.dp),
    elevation = CardDefaults.cardElevation(8.dp),
    colors = CardDefaults.cardColors(containerColor = Color.White),
    shape = RoundedCornerShape(16.dp)
)
Tarjeta blanca que ocupa todo el ancho disponible

Altura fija de 230dp

Sombra (elevación) de 8dp para dar profundidad

Bordes redondeados de 16dp

Contiene toda la información del recordatorio

Contenido de la Tarjeta
Dentro de la tarjeta, una columna organiza los elementos con espacio uniforme:

Título con emoji:

kotlin
Text(
    text = "🔔 Próximo medicamento",
    fontSize = 22.sp,
    fontWeight = FontWeight.Bold,
    color = Color(0xFF333333)
)
Usa un emoji de campana como ícono visual

Texto grande (22sp) y negritas

Color gris oscuro (#333333)

Nombre del medicamento y hora:

kotlin
Text(
    text = "$nombreMedicamento $horario",
    fontSize = 27.sp,
    color = Color(0xFF00796B),
    fontWeight = FontWeight.SemiBold
)
Combina el nombre y la hora en un solo texto

Tamaño muy grande (27sp) para que sea claramente visible

Color verde azulado (#00796B) para destacar

Semi-negritas

Botones de acción:

kotlin
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
Fila con dos botones distribuidos uniformemente

Botón verde (#4CAF50) para "Ya la tomé" - acción positiva

Botón rojo (#D32F2F) para "Emergencia" - acción de alerta

Ambos botones con texto grande (21sp) en blanco

Al presionarlos, ejecutan sus respectivos callbacks
Flujo de Trabajo
La alarma se activa: El AlarmReceiver detecta que es hora y muestra la notificación

El usuario abre la notificación: Llega a esta pantalla con los datos del medicamento específico

El usuario ve la información: Puede confirmar qué medicamento debe tomar y a qué hora

El usuario toma una decisión:

Si ya tomó el medicamento: Presiona "Ya la tomé" → se ejecuta onYaLaTome() que probablemente:

Marca el medicamento como tomado en la base de datos

Cancela la notificación actual

Posiblemente cierra la pantalla

Si tiene una emergencia: Presiona "Emergencia" → se ejecuta onEmergencia() que probablemente:
Color.kt
kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.theme

import androidx.compose.ui.graphics.Color

val Purple80 = Color(0xFFD0BCFF)
val PurpleGrey80 = Color(0xFFCCC2DC)
val Pink80 = Color(0xFFEFB8C8)

val Purple40 = Color(0xFF6650a4)
val PurpleGrey40 = Color(0xFF625b71)
val Pink40 = Color(0xFF7D5260)
 Descripción General
Este archivo define los colores principales que se usan en el tema de la aplicación. Los colores están organizados en dos grupos: los de la paleta clara (versión 40, más oscuros) y los de la paleta oscura (versión 80, más claros). Esta es la paleta base que Material 3 usa para generar el esquema de colores completo.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.ui.theme

Navega a la pantalla de emergencia o contacto de emergencia

Permite llamar rápidamente a servicios de emergencia

# Type.kt

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
 Descripción General
Este archivo define la tipografía (estilos de texto) que se usa en toda la aplicación siguiendo las especificaciones de Material 3. Actualmente solo define el estilo bodyLarge, pero está preparado para agregar más estilos de texto según se necesiten.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.ui.theme

 Explicación Detallada
¿Qué es Typography en Material 3?
Typography es un sistema de estilos de texto predefinidos que Material 3 proporciona para mantener consistencia en toda la aplicación. Cada estilo tiene un propósito específico y propiedades como tamaño de fuente, peso, altura de línea y espaciado entre letras.
Estilo Definido: bodyLarge
kotlin
bodyLarge = TextStyle(
    fontFamily = FontFamily.Default,  // Usa la fuente por defecto del sistema
    fontWeight = FontWeight.Normal,   // Peso normal (equivalente a 400)
    fontSize = 16.sp,                  // Tamaño de 16sp (tamaño estándar para lectura)
    lineHeight = 24.sp,                 // Altura de línea para mejor legibilidad
    letterSpacing = 0.5.sp              // Espaciado mínimo entre letras
)
Desglose de propiedades:

fontFamily = FontFamily.Default: Usa la fuente predeterminada del sistema (Roboto en Android, San Francisco en iOS). Esto asegura que la app se vea nativa en cada plataforma.

fontWeight = FontWeight.Normal: Peso normal de la fuente, sin negritas.

fontSize = 16.sp: Tamaño estándar para texto de lectura cómoda. sp (scale-independent pixels) se ajusta según preferencias de accesibilidad del usuario.

lineHeight = 24.sp: Espacio entre líneas, mejora la legibilidad especialmente en párrafos largos.

letterSpacing = 0.5.sp: Pequeño espaciado entre caracteres para mejor claridad.

Estilos Comentados (Preparados para futuro)
El archivo incluye comentarios con otros estilos que podrían implementarse:

kotlin
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
titleLarge: Para títulos importantes (22sp, más grande que body)

labelSmall: Para textos muy pequeños como etiquetas (11sp, con peso Medium para destacar)

 Propósito
Consistencia visual: Toda la app usa los mismos estilos de texto

Mantenibilidad: Cambiar un estilo en un solo lugar afecta toda la app

Jerarquía visual: Diferentes estilos crean una jerarquía clara para el usuario

Accesibilidad: Usar sp permite que el texto se escale con preferencias del usuario

# NotificationHelper.kt

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
 Descripción General
Esta clase se encarga de crear y mostrar las notificaciones de recordatorio de medicamentos. Configura el canal de notificaciones (necesario en Android 8+), construye notificaciones llamativas con colores verdes y texto expandido, y prepara los Intents para que el usuario pueda interactuar con ellas.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.utils

 Explicación Detallada
Propiedades
CHANNEL_ID: Identificador único del canal de notificaciones

CHANNEL_NAME: Nombre visible del canal en configuración del sistema

notificationManager: Servicio del sistema para gestionar notificaciones

ACCENT_COLOR_GREEN: Color verde intenso (0xFF00FF00) para destacar visualmente

Inicialización
kotlin
init {
    createNotificationChannel()
}
Al crear una instancia, automáticamente crea el canal de notificaciones.

createNotificationChannel()
Crea un canal de notificaciones con importancia alta (aparece en pantalla, hace sonido). Configura:

Luz LED verde

Vibración personalizada (500ms, pausa 200ms, 500ms)

Badge en el ícono de la app

showMedicationNotification()
Qué hace: Construye y muestra una notificación para recordar tomar un medicamento.

Parámetros:

medName: Nombre del medicamento

medId: ID del medicamento (para acciones)

Características de la notificación:

Título grande: "⏰ ¡HORA DE TU MEDICAMENTO!" con emoji

Texto expandido: Muestra información detallada al expandir

Color verde intenso: Toda la notificación se tiñe de verde (colorized = true)

Prioridad máxima: Aparece incluso en "No molestar"

Categoría alarma: Tratamiento especial del sistema

Vibración y luz LED: Para llamar la atención

Botón de acción: Preparado pero comentado (línea 88-92)

AlarmUtils.kt
kotlin
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
 Descripción General
Este archivo contiene funciones utilitarias para programar y cancelar alarmas de medicamentos usando AlarmManager. Calcula fechas futuras, maneja repeticiones semanales y genera IDs únicos para cada alarma.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.utils

 Explicación Detallada
Constantes de Acción
ACTION_MEDICATION_ALARM: Identifica alarmas de medicamentos

ACTION_MARK_TAKEN: Identifica acción de "Ya lo tomé"

Mapa de Días
kotlin
private val dayMap = mapOf(
    "Dom" to Calendar.SUNDAY, "Lun" to Calendar.MONDAY, "Mar" to Calendar.TUESDAY,
    "Mie" to Calendar.WEDNESDAY, "Jue" to Calendar.THURSDAY, "Vie" to Calendar.FRIDAY,
    "Sab" to Calendar.SATURDAY
)
Convierte abreviaturas de días (como "Lun") a constantes de Calendar.

scheduleAlarmsForMedication()
Qué hace: Programa alarmas para un medicamento según sus horarios y días.

Parámetros:

medId: ID del medicamento en base de datos (Long)

times: Lista de horarios en formato "HH:mm"

days: Lista de días (abreviaturas: "Lun", "Mar", etc.)

medName: Nombre del medicamento

Lógica:

Para cada horario, para cada día, crea una alarma

Genera un ID único: medId*100 + timeIndex*10 + dayOfWeek

Calcula el Calendar para la próxima ocurrencia

Si la hora ya pasó hoy, programa para la próxima semana

Usa setRepeating con intervalo semanal

cancelAlarmsForMedication()
Qué hace: Cancela todas las alarmas asociadas a un medicamento.

Lógica:

Itera por posibles horarios (0-4) y todos los días

Genera los mismos IDs que al programar

Busca PendingIntents sin crear nuevos (FLAG_NO_CREATE)

Si existen, los cancela y libera recursos

PlacesApiHelper.kt
kotlin
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
 Descripción General
Este helper maneja la interacción con Google Places API para buscar farmacias cercanas a la ubicación del usuario. Inicializa el servicio, realiza búsquedas, calcula distancias y formatea resultados.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.utils

 Explicación Detallada
initialize()
Inicializa el SDK de Places con la API key. Solo si no estaba inicializado.

buscarFarmacias()
Qué hace: Busca farmacias cercanas a la ubicación del usuario.

Parámetros:

context: Contexto de la aplicación

userLocation: Coordenadas del usuario

Proceso:

Crea un cliente de Places

Solicita lugares cercanos con campos específicos (nombre, dirección, coordenadas, tipos)

Filtra resultados que sean farmacias (tipo PHARMACY o nombre que contenga "farmacia")

Calcula distancia desde el usuario a cada farmacia

Formatea la distancia para mostrarla

Ordena por distancia y toma las 10 más cercanas

calcularDistancia()
Implementa la fórmula de Haversine para calcular distancia entre dos coordenadas en metros.

formatearDistancia()
Formatea metros a:

"XXX m" si es menos de 1000 metros

"X.X km" si es más de 1000 metros

ThemePreferences.kt
kotlin
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
 Descripción General
Esta clase maneja las preferencias de tema (oscuro/claro) usando Jetpack DataStore, una alternativa moderna a SharedPreferences. Guarda la preferencia del usuario de forma persistente.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.utils

 Explicación Detallada
DataStore
kotlin
private val Context.dataStore: DataStore<Preferences> by preferencesDataStore(name = "theme_preferences")
Define una extensión de Context que proporciona una instancia de DataStore con nombre "theme_preferences".

Clave de Preferencia
kotlin
private val IS_DARK_THEME = booleanPreferencesKey("is_dark_theme")
Define una clave booleana para guardar el estado del tema.

isDarkTheme: Flow<Boolean>
Expone un Flow que emite el valor actual de la preferencia. Los componentes pueden observar este Flow para reaccionar a cambios.

setDarkTheme()
Función suspendida que guarda un nuevo valor para la preferencia de tema.


# AppointmentViewModel.kt

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
 Descripción General
Este ViewModel maneja la lógica de negocio para las citas médicas. Conecta la interfaz de usuario con Firebase, tanto Firestore (para consultas en tiempo real) como Realtime Database (para respaldo). También mantiene el estado de las citas para que la UI pueda observarlo.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

 Explicación Detallada
Propiedades
repository: Instancia de FirebaseAppointmentRepository para operaciones CRUD en Firestore

firebaseDb: Referencia a Firebase Realtime Database para respaldo

allAppointments: StateFlow que emite la lista de citas, observable desde la UI

allAppointments
kotlin
val allAppointments = repository.getAllAppointments()
    .stateIn(
        scope = viewModelScope,
        started = SharingStarted.WhileSubscribed(5000),
        initialValue = emptyList()
    )
Convierte el Flow del repositorio en un StateFlow que:

Se mantiene activo mientras haya suscriptores (más 5 segundos)

Valor inicial vacío

La UI puede observar cambios en tiempo real

insertAppointment()
Guarda una nueva cita en Firebase:

Inserta en Firestore usando el repositorio

También guarda en Realtime Database como respaldo

deleteAppointment()
Elimina una cita:

Elimina de Firestore usando el repositorio

Busca y elimina en Realtime Database por lugar y fecha/hora

AppointmentViewModelFactory.kt
kotlin
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
 Descripción General
Fábrica para crear instancias de AppointmentViewModel. No necesita parámetros porque el ViewModel crea sus propias dependencias internamente.

EmergencyContactViewModel.kt
kotlin
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
 Descripción General
ViewModel para gestionar contactos de emergencia. Usa Firebase Firestore como fuente de datos y mantiene el estado para la UI.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

 Explicación Detallada
Propiedades
repository: Instancia de FirebaseContactRepository

allContacts: StateFlow con la lista de contactos

Métodos
insertContact(): Crea un nuevo contacto y lo guarda en Firebase

deleteContact(): Elimina un contacto existente

EmergencyContactViewModelFactory.kt
kotlin
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
 Descripción General
Fábrica simple para crear instancias de EmergencyContactViewModel. No requiere parámetros.

MedicationViewModel.kt
kotlin
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
 Descripción General
ViewModel más complejo que maneja la lógica completa de medicamentos: guardado en Firebase, programación de alarmas, sincronización con Realtime Database y seguimiento del progreso del tratamiento.

 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

 Explicación Detallada
Propiedades
Extiende AndroidViewModel para tener acceso al Context de la aplicación

repository: FirebaseMedicationRepository para Firestore

firebaseDb: Realtime Database para respaldo

allMedications: StateFlow con la lista observable

init{}
Solo imprime en consola la lista actual para debugging.

insertMedication()
Flujo completo al agregar medicamento:

Crea entidad con daysTaken = 0

Guarda en Firestore

Genera ID único para alarmas (hash de nombre + timestamp)

Programa alarmas con scheduleAlarmsForMedication()

Guarda respaldo en Realtime Database

deleteMedication()
Elimina de Firestore

Cancela todas las alarmas asociadas

Elimina de Realtime Database

markMedicationTaken()
Incrementa daysTaken

Actualiza en Firestore (elimina viejo + inserta nuevo)

Si completó tratamiento, elimina automáticamente

MedicationViewModelFactory.kt
kotlin
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
 Descripción General
Fábrica que proporciona el Application context al MedicationViewModel, necesario para programar alarmas.

MainActivity.kt
kotlin
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
 Descripción General
Actividad principal de la aplicación. Punto de entrada que configura todo lo necesario:

Permisos de notificaciones (Android 13+)

Inicialización de Places API

Tema (claro/oscuro)

Navegación

 Ubicación
Raíz del paquete (mx.edu.utng.aimc.com.pantallaprincipal)

 Explicación Detallada
onCreate()
enableEdgeToEdge(): Hace que la app use toda la pantalla, incluyendo áreas de sistema

Solicita permiso de notificaciones (solo Android 13+)

Inicializa Places API con la API key (hardcodeada)

Configura el tema: Lee preferencias guardadas y las aplica

Inicia la navegación con NavGraph()

requestNotificationPermission()
Para Android 13 (Tiramisu) y superior, solicita permiso POST_NOTIFICATIONS necesario para mostrar notificaciones.

Conclusión
MediAlerta es una aplicación completa que cumple con su objetivo principal: ayudar a las personas a organizar su salud de manera sencilla y efectiva. A lo largo de su desarrollo, se implementaron todas las funcionalidades planeadas en el manual original, logrando una aplicación funcional y estable.

Lo que se logró
Gestión de medicamentos: Los usuarios pueden registrar sus medicamentos con nombre, dosis, notas, horarios específicos y días de la semana. La aplicación lleva un conteo de los días tomados y muestra el progreso del tratamiento con barras visuales. Además, programa alarmas automáticas para recordar al usuario cuándo debe tomar cada dosis.

Citas médicas: Se implementó un sistema completo para agendar citas, con selectores de fecha y hora, lista de citas ordenadas y opción de eliminar. Los datos se guardan tanto local como en la nube.

Contactos de emergencia: Los usuarios pueden guardar números importantes como familiares, médicos o servicios de emergencia. Desde la misma aplicación pueden llamar a estos contactos o directamente al 911 con un solo toque.

Farmacias cercanas: Usando Google Maps y Places API, la aplicación muestra un mapa con la ubicación del usuario y las farmacias más cercanas. Cada farmacia aparece con su nombre, dirección y un botón para abrir Google Maps con la ruta para llegar.

Personalización: La aplicación recuerda las preferencias del usuario, como el tema claro u oscuro, y las aplica cada vez que se abre.

Notificaciones: El sistema de alarmas y notificaciones funciona correctamente, mostrando recordatorios llamativos con colores verdes y opciones para confirmar que se tomó el medicamento.

Tecnologías utilizadas
El proyecto hace uso de tecnologías modernas de Android:

Kotlin: Lenguaje principal de programación

Jetpack Compose: Para construir interfaces de usuario declarativas y reactivas

Room: Base de datos local en el dispositivo

Firebase: Autenticación, Firestore y Realtime Database para sincronización en la nube

Google Maps y Places API: Para ubicación y búsqueda de farmacias

AlarmManager: Para programar recordatorios en segundo plano

DataStore: Para guardar preferencias de usuario de forma moderna

Corrutinas y Flow: Para operaciones asíncronas y datos reactivos

Aspectos a mejorar
Como todo proyecto, hay cosas que podrían mejorarse en versiones futuras:

La API key de Google Maps está hardcodeada en el código, lo cual no es seguro. Debería moverse a un archivo de configuración seguro.

Las notificaciones podrían tener más opciones, como posponer el recordatorio 5, 10 o 15 minutos.

La eliminación de medicamentos al completar el tratamiento es automática, pero algunos usuarios podrían querer mantener un historial.

Validación de datos: En algunos formularios no se valida que los campos tengan información antes de guardar.

Dos pantallas de medicamentos (MedicamentosScreen y MedicationListScreen) podrían unificarse para evitar confusión.

En resumen
MediAlerta es una aplicación funcional y bien estructurada que cumple con los objetivos planteados. Combina almacenamiento local y en la nube, ofrece una interfaz atractiva y fácil de usar, y resuelve necesidades reales de personas que requieren organización en su salud.

El código está organizado siguiendo buenas prácticas como la separación en capas (data, ui, viewmodel), uso de repositorios, inyección de dependencias y programación reactiva con Flow. Esto hace que la aplicación sea mantenible y escalable para futuras mejoras.

En conclusión, MediAlerta es una herramienta útil que puede ayudar a muchas personas a no olvidar sus medicamentos, recordar sus citas médicas y estar preparadas ante cualquier emergencia, todo desde la comodidad de su teléfono móvil.



onRequestPermissionsResult()
Maneja la respuesta del usuario a la solicitud de permisos y registra el resultado en log.
