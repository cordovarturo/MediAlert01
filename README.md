
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

Este archivo define la pantalla para agregar una nueva cita medica. Es un formulario simple donde el usuario puede ingresar el lugar de la cita y la fecha y hora. Al presionar el boton Aceptar, la informacion se envia de vuelta a la pantalla anterior.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.citas

#### Codigo Completo

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
                label = { Text("Fecha y hora") },
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

### CitasMedicasScreen.kt

#### Descripcion General

Este archivo contiene la pantalla principal de citas medicas, donde el usuario puede ver todas sus citas programadas, agregar nuevas citas y eliminar citas existentes. Es una pantalla completa con lista interactiva y dialogos para agregar citas con selector de fecha y hora.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.citas

#### Codigo Completo

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
                title = { Text("Citas Medicas") },
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
        title = { Text("Agregar Nueva Cita Medica") },
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

### ConfiguracionScreen.kt

#### Descripcion General

Este archivo contiene la pantalla de configuracion de la aplicacion. Permite al usuario personalizar su experiencia ajustando preferencias como notificaciones, tema de colores (claro/oscuro) y gestionar contactos de emergencia. Tambien incluye un boton directo para llamar al 911 y una lista de contactos de emergencia guardados.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.configuracion

#### Codigo Completo

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
                title = { Text("Configuracion") },
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
                    label = { Text("Numero de telefono") },
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

### AgregarNumeroEmergencia.kt

#### Descripcion General

Este archivo define una pantalla simple para agregar un numero de contacto de emergencia. Es un formulario basico con dos campos (nombre y numero) y un boton rojo para guardar. A diferencia de la pantalla de configuracion que tambien tiene funcionalidad de contactos, esta parece ser una pantalla independiente especifica para esta tarea.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.emergencia

#### Codigo Completo

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
                text = "Agregar numero de emergencia",
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
                label = { Text("Numero de contacto") },
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

### FarmaciasCercanasScreen.kt

#### Descripcion General

Este archivo contiene la pantalla para buscar farmacias cercanas a la ubicacion del usuario. Es una de las pantallas mas complejas de la aplicacion porque integra varias tecnologias: permisos de ubicacion, Google Maps, y llamadas a la API de Google Places para obtener farmacias reales. La pantalla se divide en dos partes: un mapa en la mitad superior y una lista de farmacias en la mitad inferior.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.farmacias

#### Codigo Completo

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
                                    "Se necesita permiso de ubicacion",
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
            title = "Tu ubicacion"
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
                    contentDescription = "Como llegar",
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
            val address = item.optString("vicinity", "Sin direccion")

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

### MediAlertaScreen.kt

#### Descripcion General

Este archivo contiene la pantalla principal de la aplicacion, llamada MediAlerta. Es la primera pantalla que ve el usuario y funciona como un menu de navegacion central. Desde aqui puede acceder a todas las funciones principales de la aplicacion: medicamentos, citas medicas, farmacias cercanas y configuracion. Tambien incluye un boton destacado de emergencia para llamar al 911.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.home

#### Codigo Completo

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
                        title = "Configuracion",
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
            contentDescription = "Ilustracion fondo",
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

### AddMedicationScreen.kt

#### Descripcion General

Esta pantalla permite al usuario agregar un nuevo medicamento a la aplicacion. Es un formulario completo que recolecta toda la informacion necesaria: nombre, dosis, notas, dias de la semana, horarios y duracion del tratamiento. Tambien incluye la funcionalidad para programar recordatorios.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos

#### Codigo Completo

```kotlin
package mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos

import android.app.TimePickerDialog
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.ArrowBack
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
                    label = { Text("Descripcion o nota") },
                    modifier = Modifier.fillMaxWidth()
                )
            }

            item {
                OutlinedTextField(
                    value = medTotalDays,
                    onValueChange = { medTotalDays = it },
                    label = { Text("Durante cuantos dias lo tomara?") },
                    modifier = Modifier.fillMaxWidth()
                )
            }

            item {
                Text("Dias de la semana:", style = MaterialTheme.typography.titleMedium)
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

### MedicamentosScreen.kt

#### Descripcion General

Esta pantalla muestra la lista de medicamentos activos con su progreso de tratamiento. Cada medicamento tiene una barra de progreso y un boton para marcar que ya se tomo la dosis.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos

#### Codigo Completo

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
                text = "${med.daysTaken} / ${med.totalDays} dias",
                style = MaterialTheme.typography.bodyMedium
            )

            Spacer(Modifier.height(10.dp))

            Button(
                onClick = onTaken,
                modifier = Modifier.fillMaxWidth()
            ) {
                Text("Ya la tome")
            }
        }
    }
}
```

### MedicationListScreen.kt

#### Descripcion General

Esta es la pantalla principal de lista de medicamentos, con un diseño mas elaborado y atractivo. Muestra todos los medicamentos registrados con su informacion detallada y opcion de eliminar.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.medicamentos

#### Codigo Completo

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
                    label = "Dias",
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
```

### NotificacionMedicamento.kt

#### Descripcion General

Este archivo define la pantalla de notificacion que se muestra al usuario cuando es hora de tomar un medicamento. Es una pantalla simple pero muy importante, ya que es la interfaz que el usuario ve cuando recibe un recordatorio. Muestra el nombre del medicamento, la hora y dos botones de accion: Ya la tome y Emergencia.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.ui.screens.notificaciones

#### Codigo Completo

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
                    text = "Proximo medicamento",
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
                        Text("Ya la tome", fontSize = 21.sp, color = Color.White)
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

### Color.kt

#### Descripcion General

Este archivo define los colores principales que se usan en el tema de la aplicacion. Los colores estan organizados en dos grupos: los de la paleta clara (version 40, mas oscuros) y los de la paleta oscura (version 80, mas claros). Esta es la paleta base que Material 3 usa para generar el esquema de colores completo.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.ui.theme

#### Codigo Completo

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

### Type.kt

#### Descripcion General

Este archivo define la tipografia (estilos de texto) que se usa en toda la aplicacion siguiendo las especificaciones de Material 3. Actualmente solo define el estilo bodyLarge, pero esta preparado para agregar mas estilos de texto segun se necesiten.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.ui.theme

#### Codigo Completo

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

### NotificationHelper.kt

#### Descripcion General

Esta clase se encarga de crear y mostrar las notificaciones de recordatorio de medicamentos. Configura el canal de notificaciones (necesario en Android 8+), construye notificaciones llamativas con colores verdes y texto expandido, y prepara los Intents para que el usuario pueda interactuar con ellas.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.utils

#### Codigo Completo

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

        val bigTitle = "HORA DE TU MEDICAMENTO"

        val bigContent = """
            Medicamento: $medName
            
            Es momento de tomar tu dosis.
            Tu salud es nuestra prioridad.
            
            No olvides tomar tu medicamento
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
                    .setSummaryText("Tu salud es primero")
            )
            .setDefaults(NotificationCompat.DEFAULT_ALL)
            .setVibrate(longArrayOf(0, 500, 200, 500))
            .setLights(ACCENT_COLOR_GREEN, 1000, 3000)
            .build()

        notificationManager.notify(medId, notification)
    }
}
```

### AlarmUtils.kt

#### Descripcion General

Este archivo contiene funciones utilitarias para programar y cancelar alarmas de medicamentos usando AlarmManager. Calcula fechas futuras, maneja repeticiones semanales y genera IDs unicos para cada alarma.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.utils

#### Codigo Completo

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

### PlacesApiHelper.kt

#### Descripcion General

Este helper maneja la interaccion con Google Places API para buscar farmacias cercanas a la ubicacion del usuario. Inicializa el servicio, realiza busquedas, calcula distancias y formatea resultados.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.utils

#### Codigo Completo

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
                                direccion = place.address ?: "Direccion no disponible",
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

### ThemePreferences.kt

#### Descripcion General

Esta clase maneja las preferencias de tema (oscuro/claro) usando Jetpack DataStore, una alternativa moderna a SharedPreferences. Guarda la preferencia del usuario de forma persistente.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.utils

#### Codigo Completo

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

### AppointmentViewModel.kt

#### Descripcion General

Este ViewModel maneja la logica de negocio para las citas medicas. Conecta la interfaz de usuario con Firebase, tanto Firestore (para consultas en tiempo real) como Realtime Database (para respaldo). Tambien mantiene el estado de las citas para que la UI pueda observarlo.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

#### Codigo Completo

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
            .addOnSuccessListener { println("Cita guardada en Firebase Realtime DB") }
            .addOnFailureListener { println("Error guardando cita: ${it.message}") }
    }
}
```

### AppointmentViewModelFactory.kt

#### Descripcion General

Fabrica para crear instancias de AppointmentViewModel. No necesita parametros porque el ViewModel crea sus propias dependencias internamente.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

#### Codigo Completo

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

### EmergencyContactViewModel.kt

#### Descripcion General

ViewModel para gestionar contactos de emergencia. Usa Firebase Firestore como fuente de datos y mantiene el estado para la UI.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

#### Codigo Completo

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

### EmergencyContactViewModelFactory.kt

#### Descripcion General

Fabrica simple para crear instancias de EmergencyContactViewModel. No requiere parametros.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

#### Codigo Completo

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

### MedicationViewModel.kt

#### Descripcion General

ViewModel mas complejo que maneja la logica completa de medicamentos: guardado en Firebase, programacion de alarmas, sincronizacion con Realtime Database y seguimiento del progreso del tratamiento.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

#### Codigo Completo

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
                println("LISTA ACTUAL DE MEDICAMENTOS → $meds")
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
                println("Guardado en Firebase Realtime DB con exito")
            }
            .addOnFailureListener {
                println("Error guardando en Firebase: ${it.message}")
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

### MedicationViewModelFactory.kt

#### Descripcion General

Fabrica que proporciona el Application context al MedicationViewModel, necesario para programar alarmas.

#### Ubicacion

mx.edu.utng.aimc.com.pantallaprincipal.viewmodel

#### Codigo Completo

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

### MainActivity.kt

#### Descripcion General

Actividad principal de la aplicacion. Punto de entrada que configura todo lo necesario: permisos de notificaciones (Android 13+), inicializacion de Places API, tema (claro/oscuro) y navegacion.

#### Ubicacion

Raiz del paquete (mx.edu.utng.aimc.com.pantallaprincipal)

#### Codigo Completo

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
                android.util.Log.d("MainActivity", "Permiso de notificacion concedido.")
            } else {
                android.util.Log.w("MainActivity", "El permiso de notificacion fue denegado.")
            }
        }
    }
}
```

## Conclusion

MediAlerta es una aplicacion completa que cumple con su objetivo principal: ayudar a las personas a organizar su salud de manera sencilla y efectiva. A lo largo de su desarrollo, se implementaron todas las funcionalidades planeadas en el manual original, logrando una aplicacion funcional y estable.

### Lo que se logro

- **Gestion de medicamentos**: Los usuarios pueden registrar sus medicamentos con nombre, dosis, notas, horarios especificos y dias de la semana. La aplicacion lleva un conteo de los dias tomados y muestra el progreso del tratamiento con barras visuales. Ademas, programa alarmas automaticas para recordar al usuario cuando debe tomar cada dosis.

- **Citas medicas**: Se implemento un sistema completo para agendar citas, con selectores de fecha y hora, lista de citas ordenadas y opcion de eliminar. Los datos se guardan tanto local como en la nube.

- **Contactos de emergencia**: Los usuarios pueden guardar numeros importantes como familiares, medicos o servicios de emergencia. Desde la misma aplicacion pueden llamar a estos contactos o directamente al 911 con un solo toque.

- **Farmacias cercanas**: Usando Google Maps y Places API, la aplicacion muestra un mapa con la ubicacion del usuario y las farmacias mas cercanas. Cada farmacia aparece con su nombre, direccion y un boton para abrir Google Maps con la ruta para llegar.

- **Personalizacion**: La aplicacion recuerda las preferencias del usuario, como el tema claro u oscuro, y las aplica cada vez que se abre.

- **Notificaciones**: El sistema de alarmas y notificaciones funciona correctamente, mostrando recordatorios llamativos con colores verdes y opciones para confirmar que se tomo el medicamento.

### Tecnologias utilizadas

El proyecto hace uso de tecnologias modernas de Android:

- **Kotlin**: Lenguaje principal de programacion
- **Jetpack Compose**: Para construir interfaces de usuario declarativas y reactivas
- **Room**: Base de datos local en el dispositivo
- **Firebase**: Autenticacion, Firestore y Realtime Database para sincronizacion en la nube
- **Google Maps y Places API**: Para ubicacion y busqueda de farmacias
- **AlarmManager**: Para programar recordatorios en segundo plano
- **DataStore**: Para guardar preferencias de usuario de forma moderna
- **Corrutinas y Flow**: Para operaciones asincronas y datos reactivos

### Aspectos a mejorar

Como todo proyecto, hay cosas que podrian mejorarse en versiones futuras:

- La API key de Google Maps esta hardcodeada en el codigo, lo cual no es seguro. Deberia moverse a un archivo de configuracion seguro.
- Las notificaciones podrian tener mas opciones, como posponer el recordatorio 5, 10 o 15 minutos.
- La eliminacion de medicamentos al completar el tratamiento es automatica, pero algunos usuarios podrian querer mantener un historial.
- Validacion de datos: En algunos formularios no se valida que los campos tengan informacion antes de guardar.
- Dos pantallas de medicamentos (MedicamentosScreen y MedicationListScreen) podrian unificarse para evitar confusion.

### En resumen

MediAlerta es una aplicacion funcional y bien estructurada que cumple con los objetivos planteados. Combina almacenamiento local y en la nube, ofrece una interfaz atractiva y facil de usar, y resuelve necesidades reales de personas que requieren organizacion en su salud.

El codigo esta organizado siguiendo buenas practicas como la separacion en capas (data, ui, viewmodel), uso de repositorios, inyeccion de dependencias y programacion reactiva con Flow. Esto hace que la aplicacion sea mantenible y escalable para futuras mejoras.

En conclusion, MediAlerta es una herramienta util que puede ayudar a muchas personas a no olvidar sus medicamentos, recordar sus citas medicas y estar preparadas ante cualquier emergencia, todo desde la comodidad de su telefono movil.
```
