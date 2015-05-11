Dies ist ein Leitfaden zu unserem Swift Stil und den Conventionen.

Bei diesem Dokument handelt es sich um einen Versuch, Patterns zu etablieren, die den folgenden Zielen genügen:

 1. Strenge erhöhen und die Wahrscheinlichkeit von Programmierfehlern verringern
 1. Ersichtlichkeit von Absichten erhöhen
 1. Verringern des Wortaufwands
 1. Weniger Diskussionen über das Aussehen des Codes

 Bei Vorschlägen oder Hinweisen bitte erst einen Blick in die [contribution guidelines](CONTRIBUTING.md) werfen und anschließend einen pull request erstellen :zap:

 ----

#### Leerzeichen

 * Tabs, nicht Leerzeichen
 * Dateien enden mit einem Zeilenumbruch
 * Großzügige Verwendung von Leerzeilen, um den Code in logische Stücke zu unterteilen
 * Überhängende Leerzeichen vermeiden
    * gilt auch für Einrückungen in leeren Zeilen

#### Präferiere `let` an Stelle von `var`, wannimmer es möglich ist.

Verwende `let foo = …` wannimmer es Möglich ist. Nutze `var` nur dann, wenn es unbedingt nöglich ist. (Beispielsweise wenn du genau *weist*, dass sich der Wert ändern wird. z.B. in Kombination mit dem `weak` Modifier).

_Begründung:_ Die Bedeutung und der Sinn beider Schlüsselwörter ist klar. Eine *let-by-default* Politik folgt sicherer und besser lesbarer Code.

Die Verwendung von `let` garantiert und *signalisiert dem Programmierer unmissverständlich*, dass keine Änderung des Werts beabsichtigt ist. Dadurch ist es einfacher, Vermutungen darüber anzustellen, was der Sinn und Zweck des Codes ist.

Damit wird es einfacher, Schlüssfolgerungen zu ziehen und den Code zu verstehen. Wird `var` für einen Wert verwendet, der sich niemals ändern soll, so muss dies dennoch manuell geprüft und sichergestellt werden.

Demnach kann angenommen werden: Wenn `var` verwendet wird, dann handelt es sich um einen veränderlichen Wert.

#### "Force-Unwrapping" von optionalen Werten vermeiden

Wenn ein Identifikator `foo` mit dem Typ `FooType?` oder `FooType!` vorliegt, sollte kein force-unwrap verwendet werden, um den zugehörige Wert (`foo!`) zu erhalten.

Stattdessen kann diese Schreibweise verwendet werden:

```swift
if let foo = foo {
    // Verwendung des ausgepackten Werts von `foo`
} else {
    // Verarbeitung des Falls, dass der optionale Wert nil ist.
}
```
Alternativ kann die optionale Verkettung von Swift verwendet werden:

```swift
// Funktionsaufruf falls `foo` nicht nil ist. Wenn `foo` nil ist, wird das Statement ignoriert
foo?.funktionsaufrufFallsFooNichtNilIst()
```
_Begründung:_ Die explizite Verwendung von `if let` mit optionalen Werten führt zu sichererem Code. Force unwrapping ist dafür prädestiniert, zu Laufzeitfehlern zu führen.

#### implizites Unwrapping von optinonalen Werten vermeiden

Sofern es möglich ist, sollte `let foo: FooType?` und nicht `let foo: FooType!` verwendet werden, wenn `foo` nil sein kann. (Im Allgemeinen kann `?` an Stelle von `!` verwendet werden.)

_Begründung:_ Eindeutige optionale Werte führen zu sichererem Code. Optionale Werte, die implizit unwrapped haben ein hohes Potential zu Abstürzen zu führen.

#### Bevorzugung von impliziten gettern bei read-only Eigenschaften und subscripts

Auf das Schlüsselwort `get` sollte bei berechneten read-only Eigenschaften und read-only subscripts verzichtet werden, sofern es möglich ist.

Dieser Code ist zu bevorzugen:

```swift
var myGreatProperty: Int {
	return 4
}

subscript(index: Int) -> T {
    return objects[index]
}
```

Dieser sollte vermieden werden:

```swift
var myGreatProperty: Int {
	get {
		return 4
	}
}

subscript(index: Int) -> T {
    get {
        return objects[index]
    }
}
```

_Begründung:_ Der Sinn und Zweck des oberen Codes ist auch ohne `get` Schlüsselwort klar. Somit kann der Code reduziert werden.


#### Expilizite Spezifizierung der Zugriffsrechte für top-level Definitionen

Top-level Funktionen, Typen und Variablen sollten immer einen explizite Spezifikation von Zugriffsrechten erhalten.

```swift
public var whoopsGlobalState: Int
internal struct TheFez {}
private func doTheThings(things: [Thing]) {}
```

Zugriffsrechte unter dem top-level können weiterhin implizit definiert werden:

```swift
internal struct TheFez {
	var owner: Person = Joshaber()
}
```

_Begründung:_ In der Regel ist es nicht angebracht, top-level Defintionen als `internal` zu spezifizieren. Durch die explizite Angabe kann sichergestellt werden, dass sich genaue Gedanken über die Zugriffsrechte gemacht wurden. Eine erneute Angabe innerhalb der Funktion wäre redundant, da hier der Standardfall normalerweise sinnvoll ist.

#### Verknüpfen des Doppelpunkts mit dem Identifikator

Wird der Typ eines Identifikators spezifiziert, folgt direkt der Doppelpunkt. Anschließend wird - separiert mit einem Leerzeichen - der Names des Typs angefügt.

```swift
class SmallBatchSustainableFairtrade: Coffee { ... }

let timeToCoffee: NSTimeInterval = 2

func makeCoffee(type: CoffeeType) -> Coffee { ... }
```

_Begründung:_ Der Spezifikator des Typs sagt etwas über den _Identifikator_ aus und sollte somit auch an diesem Positioniert werden.

Gleiches gilt auch für die Spezifikation des Typs eines Dictionaries. Hier folgt der Doppelpunkt direkt auf den Typ des Schlüssels, gefolgt von einem Leerzeichen und dem Wert.

```swift
let capitals: [Country: City] = [ Sweden: Stockholm ]
```

#### Explizite Verwendung von `self` nur wenn es nötig ist

Wenn mit Eigenschaften von `self` gearbeitet wird, sollte auf die Referenz von `self` verzichtet werden.

```swift
private class History {
	var events: [Event]

	func rewrite() {
		events = []
	}
}
```

`self` sollte nur dann verwendet werden, wenn die Sprache es verlangt, beispielsweise in closures oder wenn es zu Bennenungskonflikten bei Parametern kommt.

```swift
extension History {
	init(events: [Event]) {
		self.events = events
	}

	var whenVictorious: () -> () {
		return {
			self.rewrite()
		}
	}
}
```

_Begründung:_ Die kapselnde Semantik von `self` wird stärker herausgestellt und macht closures bessers lesbar. Außerdem wird unnötig aufwendiger Code vermieden.
