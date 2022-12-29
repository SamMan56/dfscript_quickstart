# dfscript dsl quickstart
want to write dfscript but want something more than gui scripting?

good news! here's a dsl (domain scripting language) that allows you to write
dfscript with words! syntax highlighting! autocomplete! compiler errors! type correctness!
all those lovely things that make programming easier.

## but I don't know how to use kotlin
no problem - you're not writing in kotlin, you're writing in dfscript.
kotlin is only used as a base for structuring dfscript code, where I've added brand-new syntax on top.
knowledge of the kotlin standard library is not required & in fact should not be used at all (use dfscript functions only).

## setup
clone this project & open in your IDE of choice. I've tested this with vscode & intellij idea

### vscode
install the required extensions (kotlin & gradle for java).
wait a few minutes for everything to import & things should be good to go.

### idea
should just work out of the box

## where does script go
starter can be found in `src/main/kotlin/Main.kt`.
fill the `//TODO` with your script.

## generating json
dfscript scripts are stored as json.
executing the `gradle run` task will print the json to console.
a dedicated saving system is coming soon

## how to get started on a script
### events
like dfscript, scripts need to start with an event.
start typing its name and autocomplete should suggest one, for example

```kotlin
fun main() {
    script {
        onBuildMode {
            // event code
        }
    }
}
```

you can have multiple events in a single script.

### actions
when inside event, you can call functions to add actions to the event.
most of these functions are named after dfscript's display name.
for example, to add a cancel event:

```kotlin
fun main() {
    script {
        onBuildMode {
            cancelEvent()
        }
    }
}
```

### basic arguments
many actions need arguments inputting, such as text or numbers.
they can be created using the `text()` & `number()` functions.

```kotlin
fun main() {
    script {
        onBuildMode {
            displayChat(text("a"), text("b"))
        }
    }
}
```

### variables
variables are created with the `variable()` function.
to store and reuse these in your scripts, use the `val` keyword.

```kotlin
fun main() {
    script {
        onBuildMode {
            val count = variable<NumberType>("count")
            val count2 = variable("count2", number(2))
        }
    }
}
```
variables can be initialised with a value or left default, in which case you must add the type in the function.
a variable name to be used in the final script must also be specified.

### complex arguments
a few actions require lists & dictionaries, or even items.
there are handy constructors for these too, which will output all the required actions to create the list for you

```kotlin
fun main() {
    script {
        onBuildMode {
            val testList = list("testList", number(1), number(2), number(3))
        }
    }
}
```
these complex constructors will always output variables which you should store.
these variables can then be used in actions similar to df

```kotlin
fun main() {
    script {
        onBuildMode {
            val length = variable<NumberType>("length")
            val testList = list("testList", number(1), number(2), number(3))
            
            // calculate list length and store in length
            listLength(length, testList)
        }
    }
}
```

### client values
these are called as functions that return values.
some client values are only valid in certain events

```kotlin
fun main() {
    script {
        onBuildMode {
            displayChat(playerUUID())
            // would cause compile error if uncommented
            // displayChat(keyAction().asText())
        }
        
        onKeyPress {
            displayChat(playerUUID())
            displayChat(keyAction().asText())
        }
    }
}
```

### config values
define these like variables.
they will be added to the script automatically.

```kotlin
fun main() {
    script {
        val name = textConfigValue("name", "SamMan_")
        onBuildMode {
            displayChat(name)
        }
    }
}
```

### conditions
since I can't override the usual if implementation, a similarly named function is used:

```kotlin
fun main() {
    script {
        onBuildMode {
            `if` { equals(number(3), number(4)) }.then { 
                displayChat(text("what?"))
            }
        }
    }
}
```
note the backticks around if.
inside the `if` brackets special condition functions can be used, such as equals.
to access the if body, use the `then` function.

for more conditional logic, the `else_if` and `else` functions can be chained.

```kotlin
fun main() {
    script {
        onBuildMode {
            `if` { equals(number(3), number(4)) }.then {
                displayChat(text("what?"))
            }.else_if { equals(number(3), number(3)) }.then { 
                displayChat(text("that makes more sense"))
            }.`else` { 
                displayChat(text("what??"))
            }
        }
    }
}
```

conditions may also be negated

```kotlin
fun main() {
    script {
        onBuildMode {
            `if` { !fileExists(text("cool_file")) }.then { 
                writeFile(text("cool_file"), text("contents"))
            }
        }
    }
}
```

### repetition
the other repetition actions are much simpler.
it's normal kotlin syntax to take some arguments then open curly brackets.

```kotlin
fun main() {
    script {
        onBuildMode {
            val i = variable<NumberType>("i")
            repeatMultiple(number(5), i) {
                sendChat(i.asText())
            }
        }
    }
}
```