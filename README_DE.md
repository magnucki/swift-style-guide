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

#### Präferiere structs nichts Klassen

Sofern keine Funktionalität benötig wird, die nur von Klassen bereitgestellt werden kann (beispielsweise Identität oder Deinitialisierung), sollte ein struct implementiert werden.

Dabei sollte bedacht werden, dass Vererbung in der Regel _kein_ guter Grund ist, um Klassen zu verwenden. Polymorphismen können auch von Protokollen bereitgestellt werden und die Wiederverwendung von Implementationen wird durch Kompositionen bereitgestellt. 

Angenommen die vorliegende Hierarchie sei gegeben:

```swift
class Vehicle {
    let numberOfWheels: Int

    init(numberOfWheels: Int) {
        self.numberOfWheels = numberOfWheels
    }

    func maximumTotalTirePressure(pressurePerWheel: Float) -> Float {
        return pressurePerWheel * numberOfWheels
    }
}

class Bicycle: Vehicle {
    init() {
        super.init(numberOfWheels: 2)
    }
}

class Car: Vehicle {
    init() {
        super.init(numberOfWheels: 4)
    }
}
```

Dann kann diese wie folgt refaktorisiert werden: 

```swift
protocol Vehicle {
    var numberOfWheels: Int { get }
}

func maximumTotalTirePressure(vehicle: Vehicle, pressurePerWheel: Float) -> Float {
    return pressurePerWheel * vehicle.numberOfWheels
}

struct Bicycle: Vehicle {
    let numberOfWheels = 2
}

struct Car: Vehicle {
    let numberOfWheels = 4
}
```

_Begründung:_ Value Typen sind einfacher, besser zu verstehen und zeigen das gewünschte Verhalten in Kombination mit dem `let` Schlüsselwort

## Klassen normalweise als `final` deklarieren

Klassen sollten generell als `final` deklariert werden. Verzichtet wird darauf nur, um Vererbung zu ermöglichen, sofern dafür ein triftiger Grund vorhanden ist. Dann sollte dennoch darauf geachtet werden, dass möglichst viele definitionen _innerhalb_ der Klasse als `final` gekennzeichnet werden. 

_Begründung:_ Im Allgemeinen werden Kompositionen der Vererbung vorgezogen. Eine Entscheidung für Vererbung lässt hoffentlich darauf schließen, dass über diese Entscheidung genauer nachgedacht wurde.

## Verzicht auf Typparameter

Methoden, die auf parametrisierten Typen arbeiten, können auf Typparameter verzichten, wenn der Typ der gleiche ist, wie der des Empfängers. Beispiel:

```swift
struct Composite<T> {
    …
    func compose(other: Composite<T>) -> Composite<T> {
        return Composite<T>(self, other)
    }
}
```
kann auch so geschrieben werden:

```swift
struct Composite<T> {
    …
    func compose(other: Composite) -> Composite {
        return Composite(self, other)
    }
}
```  
_Begründung:_ Der Verzicht auf redundante Typparameter verdeutlicht den Zweck der Verwendung. Damit wird visuell klargestellt, wann der Rückgabetyp einen anderen Typparameter erwartet.

## Leerzeichen zur Abgrenzung von Operatordefinition

Wenn ein Operator definiert wird, sollte dieser mit Leerzeichen umgeben werden. Anstelle von: 

```swift
func <|(lhs: Int, rhs: Int) -> Int
func <|<<A>(lhs: A, rhs: A) -> A
```

sollte diese Schreibweise verwendet werden:

```swift
func <| (lhs: Int, rhs: Int) -> Int
func <|< <A>(lhs: A, rhs: A) -> A
```

_Begründung:_ Operatoren enthalten Zeichen, die für die Zeichensetzung bestimmt sind. Daduch sind sie nur schwer zu lesen, wenn ihnen ein Zeichen für einen Typ oder eine Parameterliste nachfolgt. Das hinzufügen von Leerzeichen sorgt für einen größeren Abstand und damit einer optischen Abgrenzung.

