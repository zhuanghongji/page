# Kotlin Examples - Delegation Pattern

## 示例

```kt
interface SoundBehaviour {                                                          // 1
    fun makeSound()
}

class ScreamBehavior(val n:String): SoundBehaviour {                                // 2
    override fun makeSound() = println("${n.toUpperCase()} !!!")
}

class RockAndRollBehavior(val n:String): SoundBehaviour {                           // 2
    override fun makeSound() = println("I'm The King of Rock 'N' Roll: $n")
}

// Tom Araya is the "singer" of Slayer
class TomAraya(n:String): SoundBehaviour by ScreamBehavior(n)                       // 3

// You should know ;)
class ElvisPresley(n:String): SoundBehaviour by RockAndRollBehavior(n)              // 3

fun main() {
    val tomAraya = TomAraya("Trash Metal")
    tomAraya.makeSound()                                                            // 4
    val elvisPresley = ElvisPresley("Dancin' to the Jailhouse Rock.")
    elvisPresley.makeSound()
}
```

```
TRASH METAL !!!
I'm The King of Rock 'N' Roll: Dancin' to the Jailhouse Rock.
```

在 Kotlin 中，很容易在没有任何样板的情况下来委托方法的调用。

1. 定义了接口 `SoundBehaviour`。后面会有一个实现了该方法的类，另一个类也实现了该接口，当仅仅是委托方法调用。
2. `ScreamBehavior` 和 `RockAndRollBehavior` 实实际际的实现了该方法。
3. `TomAraya` 和 `ElvisPresley` 类仅仅是委托了 `SoundBehaviour` 接口中定义的方法的可靠实现。但是根本不需要任何样板来委托方法调用。
4. 在 `TomAraya` 类型的 `tomAraya` 上调用 `makeSound()` 方法，或者调用类型为 `ElvisPresley` 的 `elvisPresley`，并将其委托给关联的类。
