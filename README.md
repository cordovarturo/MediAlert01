# MediAlert01
Les presento mi aplicación móvil 
Link de descarga
https://edith294.github.io/PaginawebappSalud/

Vídeo de prueba
https://drive.google.com/file/d/1xIXpM33FM7raiktjbWgTmhMpb74Ps2iN/view?usp=sharing

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
# AppointmentDao.kt

## 📋 Descripción General
Este archivo define la interfaz de acceso a datos (DAO) para la entidad `AppointmentEntity` utilizando Room, la biblioteca de persistencia de Android. Su función principal es gestionar las operaciones de la base de datos local relacionadas con las citas médicas.

---

## 📁 Ubicación
`mx.edu.utng.aimc.com.pantallaprincipal.data.dao`

---

## 🔧 Dependencias
- `androidx.room.*` - Para las anotaciones y operaciones de Room
- `kotlinx.coroutines.flow.Flow` - Para observar cambios en tiempo real
- `mx.edu.utng.aimc.com.pantallaprincipal.data.entity.AppointmentEntity` - La entidad que representa una cita

---

## 📄 Código Completo

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
