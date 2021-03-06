# NetAcademia - Bevezetés a Kotlin fejlesztésbe

## 1. Kotlin nyelv alapjai

### 1.1 Kotlin alapok

Kotlin osztály:

```kotlin
class Car constructor(type: String) {
    val typeUpper = type.toUpperCase()

    init {
        Log.d("TAG_DEMO","Car created: ${type}")
    }

    constructor(type: String, model: String) : this(type) {
        Log.d("TAG_DEMO","Car model: ${model}")
    }
}
```

Öröklés példa:

```kotlin
open class Item(price: Int) {
    open fun calculatePrice() {}
    fun load() {}
}

class SpecialItem(price : Int) : Item(price) {
    final override fun calculatePrice() {}
}
```

Data class:

```kotlin
data class Ship(val name: String, val age: Int)
```

Delegate példa:

```kotlin
interface Pressable {
    fun press()
}
class MyButton(val x: Int) : Pressable {
    override fun press() { print("press $x") }
}

class SpecialButton(pressable: Pressable) : Pressable by pressable

fun main(args: Array<String>) {
    val btn = MyButton(10)
    SpecialButton(btn).press() // press 10
}
```

Range-k:

```kotlin
val x = 4
val y = 3
if (x in 1..y+1) {
    Log.d("TAG_DEMO", "x benne van")
}
```

Range iteráció:

```kotlin
for (nr in 1..10 step 2) {
    Log.d("TAG_DEMO", "szam $nr")
}
```

Lambda műveletek kollekciókon:

```kotlin
val fruits = listOf("alma", "mango", "mandarin", "narancs")
fruits.filter { it.startsWith("m") }
        .sortedBy { it }
        .map { it.toUpperCase() }
        .forEach { Log.d("TAG_DEMO", "$it") }
 ```

### 1.2 TicTactoe példa

Singleton model osztály:

```java
object TicTacToeModel {
    public val EMPTY: Short = 0
    public val CIRCLE: Short = 1
    public val CROSS: Short = 2

    private val model = arrayOf(
            shortArrayOf(EMPTY, EMPTY, EMPTY),
            shortArrayOf(EMPTY, EMPTY, EMPTY),
            shortArrayOf(EMPTY, EMPTY, EMPTY))
    private var nextPlayer = CIRCLE.toShort()

    fun resetModel() {
        for (i in 0..2) {
            for (j in 0..2) {
                model[i][j] = EMPTY
            }
        }
        nextPlayer = CIRCLE
    }

    fun getFieldContent(x: Int, y: Int) = model[x][y]

    fun setFieldContent(x: Int, y: Int, content: Short) {
        model[x][y] = content
    }

    fun getNextPlayer() = nextPlayer

    fun changeNextPlayer() {
        nextPlayer = if (nextPlayer == CIRCLE) CROSS else CIRCLE
    }
}
```

Custom TicTacToeView osztály:

```java
class TicTacToeView : View {

    private val paintBg : Paint = Paint()
    private val paintLine : Paint = Paint()

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs) {
        paintBg.color = Color.BLACK
        paintBg.style = Paint.Style.FILL

        paintLine.color = Color.WHITE
        paintLine.style = Paint.Style.STROKE
        paintLine.strokeWidth = 5f
    }

    override fun onDraw(canvas: Canvas) {
        canvas.drawRect(0f, 0f, width.toFloat(), height.toFloat(), paintBg)

        drawGameArea(canvas)
        drawPlayers(canvas)
    }

    private fun drawGameArea(canvas: Canvas) {
        // border
        canvas.drawRect(0f, 0f, width.toFloat(), height.toFloat(), paintLine)
        // two horizontal lines
        canvas.drawLine(0f, (height / 3).toFloat(), width.toFloat(), (height / 3).toFloat(),
                paintLine)
        canvas.drawLine(0f, (2 * height / 3).toFloat(), width.toFloat(),
                (2 * height / 3).toFloat(), paintLine)

        // two vertical lines
        canvas.drawLine((width / 3).toFloat(), 0f, (width / 3).toFloat(), height.toFloat(),
                paintLine)
        canvas.drawLine((2 * width / 3).toFloat(), 0f, (2 * width / 3).toFloat(), height.toFloat(),
                paintLine)
    }

    private fun drawPlayers(canvas: Canvas?) {
        for (i in 0..2) {
            for (j in 0..2) {
                if (TicTacToeModel.getFieldContent(i, j) == TicTacToeModel.CIRCLE) {

                    // draw a circle at the center of the field

                    // X coordinate: left side of the square + half width of the square
                    val centerX = (i * width / 3 + width / 6).toFloat()
                    val centerY = (j * height / 3 + height / 6).toFloat()
                    val radius = height / 6 - 2

                    canvas?.drawCircle(centerX, centerY, radius.toFloat(), paintLine)

                } else if (TicTacToeModel.getFieldContent(i, j) == TicTacToeModel.CROSS) {
                    canvas?.drawLine((i * width / 3).toFloat(), (j * height / 3).toFloat(),
                            ((i + 1) * width / 3).toFloat(),
                            ((j + 1) * height / 3).toFloat(), paintLine)

                    canvas?.drawLine(((i + 1) * width / 3).toFloat(), (j * height / 3).toFloat(),
                            (i * width / 3).toFloat(), ((j + 1) * height / 3).toFloat(), paintLine)
                }
            }
        }
    }

    override fun onTouchEvent(event: MotionEvent): Boolean {
        if (event.action == MotionEvent.ACTION_DOWN) {

            val tX = event.x.toInt() / (width / 3)
            val tY = event.y.toInt() / (height / 3)

            if (tX < 3 && tY < 3 && TicTacToeModel.getFieldContent(tX, tY) ==
                    TicTacToeModel.EMPTY) {
                TicTacToeModel.setFieldContent(tX, tY, TicTacToeModel.getNextPlayer())
                TicTacToeModel.changeNextPlayer()
                invalidate()
            }
        }

        return super.onTouchEvent(event)
    }

}
```

XML layout:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="25dp"
    tools:context="hu.bme.aut.android.tictactoekotlin.MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!" />

    <hu.bme.aut.android.tictactoekotlin.view.TicTacToeView
        android:id="@+id/ticTacToeView"
        android:layout_width="200dp"
        android:layout_height="200dp" />

    <Button
        android:id="@+id/btnRestart"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Restart"/>

</LinearLayout>
```

MainActivity:

```java
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val ticTacToeView = findViewById(R.id.ticTacToeView) as TicTacToeView

        (findViewById(R.id.btnRestart) as Button).setOnClickListener {
            ticTacToeView.resetGame()
        }


    }

    private fun TicTacToeView.resetGame() {
        TicTacToeModel.resetModel()
        invalidate()
    }
}
```

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








