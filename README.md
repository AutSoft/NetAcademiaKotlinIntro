# NetAcademia - Bevezetés a Kotlin fejlesztésbe

## 1. Kotlin nyelv alapjai

.

## 2. Kotlin Android környezetben

#### Erőforrások letöltése

Kattints [ide](https://github.com/AutSoft/NetAcademiaKotlinIntro/raw/master/downloads/res.zip) a projekthez szükséges erőforrások letöltéséhez. A `.zip` fájl tartalmát a projekt `app\src\main\res` mappájába kell bemásolni.

## 3. Kitekintés szerver oldali fejlesztés irányába Kotlin nyelven

### 1. kódrészlet

```java
compile("com.fasterxml.jackson.module:jackson-module-kotlin")
```

### 2. kódrészlet

```java
data class ShoppingItem(
        val id: Long = -1,
        val name: String="",
        val price: Int=0)
```

### 3. kódrészlet

```java
@Entity
data class ShoppingItem(
        @Id @GeneratedValue val id: Long = -1,
        val name: String="",
        val price: Int=0)
```

### 4. kódrészlet

```java
interface MallRepository : CrudRepository<ShoppingItem, Long> {
    fun findByNameContaining(name: String): Iterable<ShoppingItem>
}
```

### 5. kódrészlet

```java
@RestController
class MallController(private val repository: MallRepository) {
    @GetMapping("/")
    fun findAll() = repository.findAll()

    @GetMapping("/search/{name}")
    fun findByName(@PathVariable name: String) = repository.findByNameContaining(name)

    @PostMapping("/add")
    fun addItem(@RequestBody shoppingItem: ShoppingItem) = repository.save(shoppingItem)
}
```

### 6. kódrészlet

```java
@SpringBootApplication
class ShoppingkotlinServerApplication {
    @Bean
    fun init(repository: MallRepository): CommandLineRunner = CommandLineRunner {
        repository.save(ShoppingItem(name = "Rubber Duck", price = 12))
        repository.save(ShoppingItem(name = "Action Man Figure", price = 1300))
        repository.save(ShoppingItem(name = "Rubber Action Man Figure", price = 1000))
    }
}

fun main(args: Array<String>) {
    SpringApplication.run(ShoppingkotlinServerApplication::class.java, *args)
}
```

### 7. kódrészlet

```java
@RunWith(SpringRunner::class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class ShoppingMallTests {

    @Test
    fun `test GET List `() {
    }

}

```

### 8. kódrészlet

```java
@RunWith(SpringRunner::class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class ShoppingMallTests {

    @Autowired
    lateinit var restTemplate: TestRestTemplate

    @Test
    fun `test GET List `() {

        val content = """
[
  {
    "id":1,
    "name":"Rubber Duck",
    "price":12
  },
  {
    "id":2,
    "name":"Action Man Figure",
    "price":1300
  },
  {
    "id":3,
    "name":"Rubber Action Man Figure",
    "price":1000
  }
]

        """

        val testContent = content
        val responseContent = restTemplate.getForEntity("/", String::class.java).body

        assertTrue(testContent==responseContent)
    }

} 
```

### 9. kódrészlet

```java
infix fun String.equalsAsJson(other:String):Boolean{
    val parser = ObjectMapper()
    val o1:List<ShoppingItem> = parser.readValue(this, object :TypeReference<List<ShoppingItem>>(){})
    val o2:List<ShoppingItem> = parser.readValue(other,object :TypeReference<List<ShoppingItem>>(){})
    return o1==o2
}
```








