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

📋 Descripción General
Este archivo es el encargado de comunicarse con la base de datos local de la aplicación, específicamente con la tabla donde se guardan las citas médicas. Usa Room, que es una herramienta que hace más fácil guardar y recuperar información en el teléfono.

📁 Ubicación
mx.edu.utng.aimc.com.pantallaprincipal.data.dao

🧩 Explicación Detallada
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

🔄 Flujo de Trabajo Típico
Guardar una cita: Cuando el usuario crea una nueva cita, la aplicación llama a insertAppointment() con los datos de la cita.

Consultar citas: La pantalla que muestra las citas está observando getAllAppointments() para tener siempre la lista actualizada.

Eliminar una cita: Cuando el usuario decide borrar una cita, la aplicación llama a deleteAppointment() con la cita que se quiere eliminar.

Actualización automática: Al modificar la base de datos, el Flow emite automáticamente la nueva lista y la pantalla se actualiza sin necesidad de recargar.

⚠️ Notas Importantes
Los métodos insertAppointment() y deleteAppointment() son suspend, lo que significa que deben ejecutarse en segundo plano para no bloquear la interfaz de usuario.

La estrategia OnConflictStrategy.REPLACE garantiza que no habrá conflictos si se intenta guardar una cita con un ID que ya existe.

El uso de Flow hace que la aplicación sea más eficiente, ya que no necesita estar haciendo consultas constantemente para verificar si los datos cambiaron.

🔗 Archivos Relacionados
AppointmentEntity.kt - Define la estructura de una cita en la base de datos

AppDatabase.kt - La base de datos principal que contiene esta tabla

AppointmentRepository.kt - El repositorio que utiliza este DAO para sus operaciones
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
