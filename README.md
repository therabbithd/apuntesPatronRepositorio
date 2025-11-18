# Conexion a API    
## Patron Repositorio
```mermaid
flowchart TD
    R(Repositorio)
    L(Local)
    RR(Remote)
    A(Api)
    R-->L 
    R-->RR
    RR-->A
```
## LocalDataSource
```kotlin
class PokemonLocalDataSource @Inject constructor():
PokemonDataSource(
    override suspend fun readAll(): Pokemon?{
        return datasource.readAll()
    }
    override suspend fun readOne(id:long): Pokemon?{
        return datasource.readOne()
    }
)
```
### Implementacion
```kotlin
class PokemonRepositoryImpl @Inject constructor(
    @RemoteDataSource private val remoteDataSource
    @LocalDataSource private val localDataSource
):PokemonRepository { 
    override suspend fun readAll(): Pokemon?{
        return datasource.readAll()
    }
    override suspend fun readOne(id:long): Pokemon?{
        return datasource.readOne()
    }
}

```
### Interfaz
```kotlin
interface PokemonDataSource{
    fun observe(): Flow<List<Pokemon>>
    suspend fun readAll():List<Pokemon>
    suspend fun readOne(id:Long):List<Pokemon>
}
```
## RemoteDataSource
### ReadAll
```kotlin
override suspend fun readAll(): List<Pokemon> {
    val response = api.readAll()
    val finalList = mutableListOf<Pokemon>
    return if (response.isSuccesful){
        val body = response.body()
        for(result in body.results){
            val remotePokemon = readOne(result.name)
            remotepokemon?.let{
                finalList.add(it)
            }
        }
        finalList
    }
    else{
        ListOf<Pokemon>()
    }
}
```
### Observe
```kotlin
override fun observe(): Flow<List<Pokemon>> {
    return flow {
        emit(listOf<Pokemon>())
        emit(readAll())
    }.shareIn(
        scope = scope
        started = SharingStarted.WhileSubscribed(5_000L)
        replay = 1
    )
}
```