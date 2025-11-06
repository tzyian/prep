**DAO (Data Access Object):**

- Defines how app interacts with the database.
    
- Contains abstract methods annotated with `@Insert`, `@Query`, `@Update`, `@Delete`.
    
- Example:
```kotlin
@Dao
interface UserDao {
    @Query("SELECT * FROM users WHERE id = :id")
    suspend fun getUser(id: Int): User
    
    @Insert
    suspend fun insertUser(user: User)
}
```

**Repository:**

- Middle layer between DAO (data source) and ViewModel (UI logic).
    
- Encapsulates data sources (DB, network, cache).
    
- Provides a clean API for the rest of the app.
    
- Example:

```kotlin
class UserRepository(private val userDao: UserDao) {
    suspend fun getUser(id: Int) = userDao.getUser(id)
    suspend fun insertUser(user: User) = userDao.insertUser(user)
}
```

### 2. **ViewModel**

Part of Android Jetpack’s architecture components.

- Holds **UI-related data** that survives configuration changes (e.g. screen rotation).
    
- Avoids putting logic in Activities/Fragments.
    
- Tied to lifecycle but **outlives** the activity until it’s finished.

```kotlin
class UserViewModel(private val repository: UserRepository) : ViewModel() {
    private val _user = MutableLiveData<User>()
    val user: LiveData<User> = _user

    fun loadUser(id: Int) {
        viewModelScope.launch {
            _user.value = repository.getUser(id)
        }
    }
}
```

Improves testability, modularity, and allows switching data sources without touching the UI layer.


### 3. **Activity Lifecycle**

**Activity** = a full screen in your app.  
**Fragment** = a reusable section _within_ an Activity.

|Stage|Purpose|Analogy to web|
|---|---|---|
|**onCreate()**|Inflate layout, init data, set observers.|`DOMContentLoaded` / JS init|
|**onStart()**|UI becomes visible but not interactive.|Page shown but not focused|
|**onResume()**|Foreground, user can touch and type.|Page gains focus|
|**onPause()**|Another window partly covers it; save small state, stop animations.|`visibilitychange` → hidden|
|**onStop()**|Fully hidden; release camera, sensors, or other heavy resources.|Tab backgrounded|
|**onDestroy()**|Activity about to be killed. Clean up references to avoid leaks.|Page unload / tab closed|
