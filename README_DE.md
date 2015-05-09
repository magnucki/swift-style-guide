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


#### Expilizite Spezifizierung der Zugriffsrechte für Definitionen der höchsten Ebene
