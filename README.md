
```markdown
# MediAlert01

## Introduccion

MediAlerta es una aplicacion movil desarrollada para Android que tiene como objetivo ayudar a las personas a llevar un control mas organizado de su salud. La aplicacion nace de la necesidad de muchas personas que toman medicamentos de forma regular, tienen citas medicas frecuentes o simplemente quieren estar preparados ante cualquier emergencia.

La aplicacion permite a los usuarios registrar sus medicamentos con sus respectivos horarios y dosis, programar citas medicas, guardar contactos de emergencia y localizar farmacias cercanas a su ubicacion. Todo esto en un solo lugar, con una interfaz colorida y facil de usar que no requiere conocimientos tecnicos.

El desarrollo de MediAlerta se realizo utilizando Kotlin como lenguaje de programacion principal y Jetpack Compose para construir la interfaz de usuario moderna y reactiva. Los datos se almacenan tanto localmente en el dispositivo usando Room, como en la nube usando Firebase, lo que permite que la informacion no se pierda aunque se desinstale la aplicacion o se cambie de telefono.

El equipo detras de MediAlerta esta conformado por Colin Cardenas Kelly Anahi y Mendez Galvan Claudia Lizbeth, quienes disenaron y programaron cada una de las funcionalidades pensando en hacer la vida mas facil a las personas que necesitan recordatorios constantes para su salud.

## Link de descarga

https://edith294.github.io/PaginawebappSalud/

## Video de prueba

https://drive.google.com/file/d/1xIXpM33FM7raiktjbWgTmhMpb74Ps2iN/view?usp=sharing

## Descripcion de Carpetas

### data/

Contiene todo lo relacionado con el manejo de datos de la aplicacion, incluyendo la base de datos local y las fuentes de datos.

- **dao/**: Objetos de acceso a datos para interactuar con Room
- **database/**: Configuracion de la base de datos Room y convertidores
- **entity/**: Clases que representan las tablas de la base de datos
- **model/**: Modelos de datos para la logica de negocio
- **repository/**: Repositorios que manejan las fuentes de datos (local y remota)

### navigation/

Gestiona la navegacion entre las diferentes pantallas de la aplicacion usando Jetpack Compose Navigation.

### receivers/

Contiene los BroadcastReceivers para manejar eventos del sistema como las alarmas de los recordatorios.

### ui/

Toda la interfaz de usuario de la aplicacion construida con Jetpack Compose.

- **screens/**: Las diferentes pantallas de la aplicacion organizadas por funcionalidad
  - **citas/**: Pantallas para gestionar citas medicas
  - **configuracion/**: Pantalla de configuracion de la aplicacion
  - **emergencia/**: Pantallas para gestionar contactos de emergencia
  - **farmacias/**: Pantalla para buscar farmacias cercanas
  - **home/**: Pantalla principal de la aplicacion
  - **medicamentos/**: Pantallas para gestionar medicamentos
- **notificaciones/**: Componentes para manejar notificaciones
- **theme/**: Configuracion del tema visual (colores, tipografia, estilos)
- **utils/**: Utilidades y helpers para la interfaz

### viewmodel/

ViewModels que separan la logica de negocio de la interfaz de usuario y manejan el estado de las pantallas.

### Archivos Principales

- **MainActivity.kt**: Actividad principal que inicia la aplicacion
- **AndroidManifest.xml**: Configuracion de la aplicacion

## Tecnologias Utilizadas

- **Kotlin**: Lenguaje de programacion principal
- **Jetpack Compose**: Para la interfaz de usuario moderna
- **Room**: Base de datos local
- **Firebase**: Para autenticacion, base de datos en la nube y notificaciones
- **Google Maps**: Para ubicacion de farmacias cercanas
- **Corrutinas**: Para operaciones asincronas
- **Flow**: Para datos reactivos
- **ViewModel**: Para manejar el estado de la UI
- **Navigation Component**: Para la navegacion entre pantallas
- **AlarmManager**: Para programar recordatorios y notificaciones

## Documentacion de Componentes Clave

### AppointmentDao.kt

#### Descripcion General

Este archivo define la interfaz de acceso a datos (DAO) para la entidad AppointmentEntity utilizando Room, la biblioteca de persistencia de Android. Su funcion principal es gestionar las operaciones de la base de datos local relacionadas con las citas medicas.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.dao

#### Dependencias

- androidx.room.* - Para las anotaciones y operaciones de Room
- kotlinx.coroutines.flow.Flow - Para observar cambios en tiempo real
- mx.edu.utng.aimc.com.pantallaprincipal.data.entity.AppointmentEntity - La entidad que representa una cita

#### Codigo Completo

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.dao

import androidx.room.*
import kotlinx.coroutines.flow.Flow
import mx.edu.utng.aimc.com.pantallaprincipal.data.entity.AppointmentEntity

/**
 * Data Access Object (DAO) para la gestion de citas medicas.
 * 
 * Proporciona metodos para insertar, eliminar y consultar citas
 * en la base de datos local utilizando Room.
 */
@Dao
interface AppointmentDao {

    /**
     * Inserta una nueva cita en la base de datos.
     * 
     * Si ya existe una cita con el mismo ID, sera reemplazada
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
     * se actualiza automaticamente.
     * 
     * @return Flow que emite una lista de AppointmentEntity ordenada
     *         por fecha/hora ascendente (de la mas proxima a la mas lejana)
     */
    @Query("SELECT * FROM appointments ORDER BY dateTime ASC")
    fun getAllAppointments(): Flow<List<AppointmentEntity>>
}
```

### EmergencyContactDao.kt

#### Descripcion General

Este archivo es el encargado de comunicarse con la base de datos local de la aplicacion, especificamente con la tabla donde se guardan los contactos de emergencia. Usa Room para facilitar el guardado y recuperacion de esta informacion en el telefono.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.dao

#### Codigo Completo

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

### MedicationDao.kt

#### Descripcion General

Este archivo es el encargado de comunicarse con la base de datos local de la aplicacion, especificamente con la tabla donde se guardan los medicamentos. Usa Room para facilitar el guardado y recuperacion de esta informacion en el telefono.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.dao

#### Codigo Completo

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

### AppDatabase.kt

#### Descripcion General

Este archivo define la base de datos local de la aplicacion usando Room. Es el corazon del almacenamiento de datos en el telefono, donde se guardan todos los registros de medicamentos, citas medicas y contactos de emergencia. Actua como el punto central que conecta todas las tablas con sus respectivos DAOs.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.database

#### Codigo Completo

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

### Converters.kt

#### Descripcion General

Este archivo contiene una clase especial llamada Converters que sirve como traductor entre tipos de datos que Kotlin maneja facilmente y tipos que la base de datos SQLite puede guardar. En particular, se encarga de convertir listas de texto en algo que se pueda almacenar en una sola celda de la base de datos y viceversa.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.database

#### Codigo Completo

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

### AppointmentEntity.kt

#### Descripcion General

Este archivo define la estructura de como se guardan las citas medicas en la base de datos del telefono. Es como un molde que le dice a la aplicacion que informacion debe guardar de cada cita y de que tipo es cada dato. Cada vez que el usuario agrega una cita, se crea un objeto de este tipo para almacenarla.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.entity

#### Codigo Completo

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

### EmergencyContactEntity.kt

#### Descripcion General

Este archivo define la estructura de como se guardan los contactos de emergencia en la base de datos del telefono. Es una entidad simple pero fundamental para la funcionalidad de emergencias de la aplicacion.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.entity

#### Codigo Completo

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

### MedicationEntity.kt

#### Descripcion General

Este archivo define la estructura de como se guardan los medicamentos en la base de datos del telefono. Es el molde mas completo de la aplicacion porque los medicamentos tienen mucha informacion asociada: el nombre, la dosis, las notas, los horarios, los dias de la semana y el progreso del tratamiento. Cada vez que el usuario agrega un nuevo medicamento, se crea un objeto de este tipo para almacenarlo.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.entity

#### Codigo Completo

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

### Medication.kt

#### Descripcion General

Este archivo define un modelo de datos simplificado para representar un medicamento en las capas superiores de la aplicacion. A diferencia de MedicationEntity, este modelo no tiene anotaciones de Room y esta pensado para usarse cuando se necesita una representacion mas ligera de un medicamento, por ejemplo en listas o vistas resumidas.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.model

#### Codigo Completo

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.data.model

data class Medication(
    val id: Int = 0,
    val name: String = "",
    val dose: String = "",
    val hour: String = ""
)
```

### AppointmentRepository.kt

#### Descripcion General

Este archivo define el repositorio de citas medicas, que actua como un puente entre la base de datos local y el resto de la aplicacion. Su trabajo es proporcionar una forma limpia y organizada de acceder a los datos de las citas sin que las otras partes de la aplicacion tengan que saber como estan guardados internamente.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.repository

#### Codigo Completo

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

### EmergencyContactRepository.kt

#### Descripcion General

Este archivo define el repositorio de contactos de emergencia, que actua como un intermediario entre la base de datos local y el resto de la aplicacion. Su funcion es proporcionar una forma organizada y limpia de acceder a los datos de los contactos de emergencia sin que las otras partes de la aplicacion tengan que saber como estan guardados internamente.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.repository

#### Codigo Completo

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

### FirebaseAppointmentRepository.kt

#### Descripcion General

Este archivo define el repositorio de citas medicas que se sincroniza con Firebase, la base de datos en la nube de Google. A diferencia del AppointmentRepository que guarda las citas localmente en el telefono, este repositorio guarda la informacion en internet, lo que permite que los datos esten disponibles en cualquier dispositivo y no se pierdan si se desinstala la aplicacion o se cambia de telefono.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.repository

#### Codigo Completo

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

### FirebaseContactRepository.kt

#### Descripcion General

Este archivo define el repositorio de contactos de emergencia que se sincroniza con Firebase, la base de datos en la nube de Google. A diferencia del EmergencyContactRepository que guarda los contactos localmente en el telefono, este repositorio guarda la informacion en internet, permitiendo que los contactos de emergencia esten disponibles en cualquier dispositivo y no se pierdan si se desinstala la aplicacion o se cambia de telefono.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.repository

#### Codigo Completo

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

### FirebaseMedicationRepository.kt

#### Descripcion General

Este archivo define el repositorio de medicamentos que se sincroniza con Firebase. Permite guardar en la nube toda la informacion de los medicamentos que el usuario registra, incluyendo nombre, dosis, notas, horarios, dias y progreso del tratamiento.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.repository

#### Codigo Completo

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

### MedicationRepository.kt

#### Descripcion General

Este archivo define el repositorio local de medicamentos, que actua como un puente entre la base de datos local (Room) y el resto de la aplicacion. Es la version local que complementa al FirebaseMedicationRepository para la nube.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.data.repository

#### Codigo Completo

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

### NavGraph.kt

#### Descripcion General

Este archivo define el sistema de navegacion de toda la aplicacion. Es como el mapa que conecta todas las pantallas entre si, permitiendo al usuario moverse de una pantalla a otra de manera organizada. Tambien se encarga de crear y proporcionar los ViewModels y repositorios que las pantallas necesitan para funcionar.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.navigation

#### Codigo Completo

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

### AlarmReceiver.kt

#### Descripcion General

Este archivo define el AlarmReceiver, que es un componente especial de Android que escucha las alarmas programadas y responde cuando estas se activan. Es como un despertador que, cuando llega la hora, se encarga de mostrar una notificacion para recordarle al usuario que debe tomar su medicamento y tambien maneja las acciones que el usuario realiza sobre esa notificacion.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.receivers

#### Codigo Completo

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
        android.util.Log.d("AlarmReceiver", "Medicamento $medId marcado como tomado")
    }

    private fun cancelNotification(context: Context, notificationId: Int) {
        val notificationManager = context.getSystemService(Context.NOTIFICATION_SERVICE) as android.app.NotificationManager
        notificationManager.cancel(notificationId)
    }
}
```

### AgregarCita.kt

#### Descripcion General

Este archivo define la pantalla para agregar una nueva cita medica. Es un formulario simple donde el usuario puede ingresar el lugar de la cita y la fecha y hora. Al presionar el boton Aceptar, la inform
