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