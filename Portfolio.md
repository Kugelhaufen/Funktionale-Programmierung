# E-Portfolio: Prüfungsleistung Funktionale Programmierung
## Daniel Spahn

Dieses E-Portfolio wurde von Daniel Spahn erstellt. Es dient als Prüfungsleistung für das Modul "Funktionale Programmierung" (Wintersemester 2022/2023) an der Technischen Hochschule Mittelhessen (THM) gemäß der [Aufgabenstellung (v1.1 2022-11-08)](https://www.dbrunner.de/lehre/fp-portfolio/)

Dieses Portfolio, sowie die Lösung für Aufgabe 1 von Block 3 ist auf GitHub zu finden.

Portfolio: https://github.com/Kugelhaufen/funktionale-programmierung

Aufgabe 3.1 (Minesweeper): https://github.com/Kugelhaufen/Racket-Minesweeper

## Aufgabenblock 1
Dieser Aufgabenblock wurde in Zusammenarbeit mit Julian Glänzer und Ari Diehl erstellt.

### Aufgabe 1.1
>Welche Ausdrücke sind strikt? Begründen Sie Ihre Antwort.
>```Scheme
>(define (square x) (* x x))
>
>; 1
>(square 9)
>
>; 2
>(if (= 10 2) (square 10) (square 0))
>
>; 3
>(and (= 10 2) (< 2 10))
>```

1.  Der Ausdruck ist strikt, da das Argument `9` ein Literal ist und nicht weiter ausgewertet werden kann.

2. Der Ausdruck ist nicht strikt da bei einer If-Condition/Bedingung abhängig von dem ersten Ausdruck (Test-Ausdruck) der vordere (Wahr-Ausdruck) oder der hintere (Falsch-Ausdruck) ausgewertet wird. Da `(= 10 2)` falsch ist, wird der hintere Ausdruck ausgewertet

3. Der Ausdruck ist nicht strikt. Der erste Ausdruck ist (= 10 2) ist falsch. Dementsprechend ist `(and (= 10 2) (< 2 10))` falsch. Die Auswertung wird abgebrochen und `(< 2 10)` wird nicht ausgewertet

### Aufgabe 1.2
> Welche der folgenden Ausdrücke sind Listen? Begründen Sie Ihre Antwort.
> ```Scheme	
> 1: '(1 (2 3) . 4)
> 2: (list 1 2 3)
> 3: (cons 1 (cons 2 (cons 3 '())))
> 4. (cons 'a 'b)
> ````

In Racket kann mit `list?` geprüft werden ob es sich um eine List handelt.
1. Ist keine Liste. `(list? '(1 (2 3) . 4))` ist false. In Racket werden die beiden Werte von einem Paar mit einem Punkt "." getrennt. Es handelt sich hierbei also um ein Paar aus `1 (2 3)` und `4`. Die `pair?` Funktion ist in diesem Fall true.

2. Ist eine Liste. `(list? (list 1 2 3))` ist `true`. Die `list` Funktion kann genutzt werden, um aus einer unbestimmten Menge an Argumenten eine neue List zu erstellen.

3. Ist eine Liste. `(list? (cons 1 (cons 2 (cons 3 '()))))` ist `true`. Die `cons` Funktion kann genutzt werden um eine neues Element an den Anfang einer List hinzuzufügen. In diesem Fall wird erst 3 einer leeren Liste hinzugefügt `(cons 3 '())`, danach 2 und als letztes 1.

4. Ist keine List. `(list? (cons 'a 'b))` ist `false`. `(cons 'a 'b)` ist ein Paar. `(pair? (cons 'a 'b))` ist true.

### Aufgabe 1.3

> Gegeben folgender Code:
> ```Scheme
>(define (p) (p))
>(define (test x y)
>  (if (= x 0)
>      0
>      y))
>```
>Was erhält der Nutzer, wenn er ‘(test 0 (p))‘ eingibt, bei applicative order, bei normal order?

Im Falle der Applicative Order wird `(p)` zuerst ausgewertet. Es handelt sich bei `p` jedoch um eine Endlosrekursion. Dies führt zu einem Stack Overflow.
Bei Normal Order wird `(p)` nicht ausgewertet (somit auch keine Endlosschleife). Stattdessen erhält der Nutzer 0, da `(= 0 0)` wahr ist. `(test 1 (p))` würde, zum Beispiel, auch bei Normal Order in die Endlosschleife geraten, da `(= 1 0)` falsch ist und daraufhin `(p)` ausgewertet wird.

### Aufgabe 1.4
>Ein Nutzer schreibt sich ein if neu, da es ja cond gibt:
>```Scheme
>(define (my-if predicate then-clause else-clause)
>  (cond (predicate then-clause)
>        (else else-clause)))
>```
>Erste Versuche zeigen: Das funktioniert auch.
>```Scheme
>(my-if (= 2 3) 0 5)
>5
>(my-if (= 1 1) 0 5)
>0
>```
>Funktioniert new-if nun wie if oder gibt es noch ein Problem? Wenn ja, welches?

`new-if` ist nicht vollständig funktionsgleich mit `if`, da bei `new-if` die Applicative Order gilt. Somit werden bei `new-if` immer alle Ausdrücke ausgewertet. Dies könnte beispielsweise sehr problematisch werden, wenn mit Seiteneffekten gearbeitet wird (z.B. I/O).

Beispiel:
```Scheme	
#lang racket
(define (new-if predicate then-clause else-clause)
  (cond (predicate then-clause)
        (else else-clause)))

(if #f
    (display "if-> unwanted side effect ")
    (display "if-> wanted side effect "))

(newline)

(new-if #f
        (display "new-if-> unwanted side effect ")
        (display "new-if-> wanted side effect "))

;if only produces the wanted side effect but new-if produces both wanted and unwanted side effects
```	

### Aufgabe 1.5
>Was macht die folgende Funktion und wie funktioniert die Auswertung:
>```Scheme
>(define (a-plus-abs-b a b)
>  ((if (> b 0) + -) a b))
>```

Der Funktion `a-plus-abs-b` addiert den Wert des ersten Arguments `(a)` mit dem Absoluten Wert des zweiten Arguments `(b)`. Der Absolute Wert einer Zahl (in Programmbibliotheken oft als `abs` abgekürzt ) ist ihr Betrag ohne Vorzeichen.
In diesem Fall wird geprüft, ob b kleiner als 0 ist. Falls ja wird b von a subtrahiert, ansonsten wird b zu a addiert.

### Aufgabe 1.6
> Umdrehen einer Liste: Schreiben Sie eine Funktion my-reverse, die eine Liste erhält und die Elemente umdreht, also bspw. `(my-reverse ’(1 2 3))` führt zu `(3 2 1)`.
```Scheme
#lang racket

(define (my-reverse lst)
  (if (null? lst)
      '()
      (append (my-reverse (cdr lst)) (list (car lst)))))

;Usage
(my-reverse '(1 2 3)) ;Output: '(3 2 1)
```

### Aufgabe 1.7
> Finden in einer Liste: Schreiben Sie eine Funktion my-find, die eine Liste lst und ein Argument x erhält und mittels equal? prüft, ob das Element in der Liste enthalten ist. Die Funktion soll #t zurückgeben, wenn dem so ist, oder #f wenn nicht.

```Scheme
#lang racket

(define (my-find lst x)
  (if (null? lst)
      #f
      (if (equal? (car lst) x)
          #t
          (my-find (cdr lst) x))))

;Usage
(my-find '(1 44 123 2 187 3) 2) ;Ouput: #t
```

### Aufgabe 1.8
> Quadrieren der Elemente einer Liste: Schreiben Sie eine Funktion square-my-list, die eine Liste mit Zahlen erhält und eine neue Liste der Quadrate dieser Zahlen zurückliefert.

```Scheme
#lang racket

(define (square-list lst)
  (map sqr lst))

;Usage
(square-list '(1 2 3 4 5)) ;Output: '(1 4 9 16 25)
```

### Aufgabe 1.9
> Addieren der Werte in einer Liste: Schreiben Sie eine Funktion sum-my-list, die eine Liste mit Zahlen erhält und diese aufaddiert.

```Scheme
#lang racket

(define ( sum-my-list l)
  (if (null? l)
      0
      (+ (car l) ( sum-my-list (cdr l)))))

;Usage
( sum-my-list '(1 2 3)) ;Ouput: 6
```

### Aufgabe 1.10
>Schreiben Sie endrekursive Fassungen der beiden letzten Funktionen.

```Scheme
#lang racket
;1.8 ->

(define (square-list lst)
  (define (sqrIteration lst accumulator)
    (if (null? lst)
        accumulator
        (sqrIteration (cdr lst) (append accumulator (list (sqr (car lst)))))))
  (sqrIteration lst '()))

;Usage
(square-list '(1 2 3 4 5)) ;Ouput: '(1 4 9 16 25)

;1.9 ->
(define (sum lst)
  (define (sumIteration lst accumulator)
    (if (null? lst)
        accumulator
        (sumIteration (cdr lst) (+ accumulator (car lst)))))
  (sumIteration lst 0))

;Usage
(sum '(1 2 3)) ;Output: 6
```

### Aufgabe 1.11
>Schreiben Sie map unter Verwendung von foldl/foldr (wählen Sie eine geeignete fold-Variante).

```Scheme
#lang racket

(define (cool-map proc lst)
  (foldl (lambda (a acc) (append acc (list (proc a)))) '() lst))

;Examples
(cool-map sqr '(1 2 3 4 5)) ;Output: '(1 4 9 16 25)
(map sqr '(1 2 3 4 5)); Output: '(1 4 9 16 25)

(cool-map (lambda (x) (display x) x) '(1 2 3 4 5)) ;Output: 12345'(1 2 3 4 5)
(map (lambda (x) (display x) x) '(1 2 3 4 5)) ;Output: 12345'(1 2 3 4 5)
```

### Aufgabe 1.12
>Wie erkennen Sie endrekursive Funktionen in Racket und welchen Vorteil haben diese?

Eine Endlosrekursive Funktion erkennt man (in Racket) daran, dass die letzte Operation innerhalb der von sich selbst ist.
Somit kann der Stack wiederverwendet werden und es muss nicht für jede Iteration ein neuer Stack erzeugt werden. 
Das ist effizienter und zudem kann dadurch kein Stack Overflow auftreten [^1]

[^1]: https://www.inf.hs-flensburg.de/lang/prog/endrekursion-formal.htm (10.11.2022)

Beispiele siehe Aufgabe 1.8), 1.9) und 1.10)

### Aufgabe 1.13
>Wann liefern foldr und foldl das gleiche Ergebnis? Geben Sie jeweils ein selbst gewähltes Beispiel für beide Fälle (ergibt das gleiche, ergibt etwas anderes) an. Wie unterscheiden sich beide Funktionen in ihrem Laufzeitverhalten (Geschwindigkeit, Speicherbedarf etc.)?

`(foldl f e lst)` und `(foldr f e lst)` liefern dann die identischen Ergebnisse, wenn `f` assoziativ ist und `e` als neutrales Element hat, dies
ist das sogenannte erstes Dualitätsgesetz." [^2]

[^2]: "04-funktionen-hoeherer-ordnung" (Vorlesungsunterlagen) Seite 15

Beispiel:

```Scheme	
#lang racket

;nicht assoziativ
(foldl cons '() '(1 2 3 4 5)) ;Output: '(5 4 3 2 1)
(foldr cons '() '(1 2 3 4 5)) ;Output: '(1 2 3 4 5)

;assoziativ
(foldl + 0 '(1 2 3 4 5)) ;Output: 15
(foldr + 0 '(1 2 3 4 5)) ;Output: 15
```	

Hinsichtlich des Laufzeitverhaltens ist festzuhalten, dass "foldr" eine
höhere Speicherkomplexität aufweist. Zurückführen lässt sich dies auf die
Tatsache, dass "foldr" mit dem letzten Element der Liste beginnt und somit
die Auswertung erst beendet werden kann, wenn die gesamte Liste traversiert
wurde.

### Aufgabe 1.14
>Was ist der Unterschied zwischen foldl und foldr? Wann ist welche Funktion sinnvoll?

Es handelt sich bei einer Funktion um eine Funktion höherer Ordnung, wenn in diese eine Funktion als Parameter eingesetzt werden kann und/oder wenn diese eine Funktion zurückgibt.
Beispiel:
```Scheme	
#lang racket

;Beispiel für eine Funktion höherer Ordnung, die eine Funktion als Argument nimmt:
;"time-apply-but-worse" ist eine Funktion, die eine Funktion und ihre Argumente als Argument nimmt
;und die Anzahl der Millisekunden (System Zeit) zurückgibt,
;die zum Ausführen der bereitgestellten Funktion benötigt werden
;(Ähnlich wie "time-apply", nur in schlecht)

(define (time-apply-but-worse f args)
  (define start (current-milliseconds))
  (apply f args)
  (- (current-milliseconds) start))

;Usage:
(define (cool-func) (sleep 2))
(time-apply-but-worse cool-func '()) ;Output ≈ 2000


;Beispiel für eine Funktion höherer Ordnung, die eine Funktion als Rückgabewert hat
;Die folgende Funktion nimmt einen Dateipfad als Parameter und gibt eine Funktion zurück, die überprüft ob die Datei existiert
(define (file-check file-path)
  (lambda () (file-exists? file-path)))

(define check-important-file (file-check "C:\\Users\\important-file.txt"))
(check-important-file)
```

### Aufgabe 1.15
>Überführen Sie die Funktion sum in eine endrekursive Fassung. Die Funktion wurde wie folgt definiert:
>```Scheme
>(define (sum term a next b)
>(cond
>((> a b) 0)
>(else (+ (term a)
>         (sum term (next a) next b)))))
>```

```Scheme
#lang racket

(define (get-num x) x)
(define (get-next-index x) (+ x 1))


;Original
(define (sum term a next b)
  (cond
    ((> a b) 0)
    (else (+ (term a)
             (sum term (next a) next b)))))

(sum get-num 1 get-next-index 10) ;Output: 55

;Endrekursiv
(define (sum-end term a next b)
  (define (sum-end-iter a acc)
    (if (> a b)
        acc
        (sum-end-iter (next a) (+ (term a) acc))))
  (sum-end-iter a 0))

(sum-end get-num 1 get-next-index 10) ;Output: 55
```

### Aufgabe 1.16
>Schreiben Sie eine Funktion ‘repeated, die eine Funktion f und eine natürliche Zahl n erhält und dann f n-mal ausführt. Beispiel:
>```Scheme
>((repeated sqr 2) 3)
>81
> 
>((repeated add1 10) 1)
>11
>```

```Scheme
#lang racket

;"repeated" als endrekursive Funktion implementiert:
(define (repeated f n)
  (lambda (x)
    (define (repeated-iter acc n)
      (if (<= n 0)
          acc
          (repeated-iter (f acc) (- n 1))))
    (repeated-iter x n)))


((repeated sqr 2) 3) ;Output: 81
((repeated add1 10) 1) ;Output: 11
(newline)


;Extras...


;Die erste Implementierung erfüllt die Anforderungen der Aufgabenstellung insofern,
;dass die gegeben Beispiele funktionieren.

;Ich habe mich jedoch beim schreiben der Funktion gefragt, ob auch eine Implementierung möglich ist,
;die auch funktioniert, wenn "f" mehrere Parameter hat.
;Z.B.: ((better-repeat + 10) 0 5)

;Daraufhin ist folgende Implementierung entstanden:

(define (repeated-multi-args f n)
  (lambda (arg . args)
    (define (repeated-iter acc n)
      (if (<= n 0)
          acc
          (repeated-iter (apply f (cons acc args)) (- n 1))))
    (repeated-iter arg n)))


((repeated-multi-args sqr 2) 3) ;Output: 81
((repeated-multi-args add1 10) 1) ;Output: 11

((repeated-multi-args + 10) 0 5) ;Output: 50
; ((repeated-multi-args map 2) sqr '(1 2 3 4)) ;Fehler!
(newline)


;Bei "repeated-multi-args" kann "f" mehrere Parameter haben.

;Jedoch fällt auf, dass diese Implementierung nur funktioniert,
;wenn der Rückgabewert von "f" in den ersten Parameter von "f" eingesetzt werden kann
;(und das auch so zum gewünschten Ergebnis führt)

;Bei "map" z.B. ist das nicht der Fall


;Da ich auch Funktionen wie "map" wiederholt anwenden wollte, ist folgende Implementierung entstanden:

(define (repeated-multi-args-dynamic f n acc-argument-index)
  (lambda (arg . args)
    (define (repeated-iter acc n)
      (if (<= n 0)
          acc
          (repeated-iter (apply f (list-set (cons arg args) acc-argument-index acc)) (- n 1))))
    (repeated-iter (list-ref (cons arg args) acc-argument-index) n)))


((repeated-multi-args-dynamic sqr 2 0) 3) ;Output: 81
((repeated-multi-args-dynamic add1 10 0) 1) ;Output: 11

((repeated-multi-args-dynamic + 10 0) 0 5) ;Output: 50
((repeated-multi-args-dynamic map 2 1) sqr '(1 2 3 4)) ;Output: '(1 16 81 256)

;Bei repeated-multi-args-dynamic gibt man mit "acc-argument-index" an,
;in welchen Parameter der Funktion "f" der Rückgabewert von "f" eingesetzt werden soll
```

## Aufgabenblock 2
Dieser Aufgabenblock wurde alleine gelöst. (Ausnahme: Aufgabe 2.7 mit Julian Glänzer)

### Aufgabe 2.1
> Schreiben Sie eine Funktion insert-sort, die eine übergebene Liste von Zahlen mittels Sortieren durch Einfügen sortiert und das Ergebnis zurückliefert. Beispiele:
> ```Scheme
>> (insert-sort '(2 4 1 6 4))
>'(1 2 4 4 6)
> (insert-sort '())
>'()
> ```

```Scheme
#lang racket

;insert-at Funktion von: https://stackoverflow.com/a/16631157
(define (insert-at lst pos x)
  (define-values (before after) (split-at lst pos))
  (append before (cons x after)))

;Geht von hinten (letzter Index) nach vorne in der Liste lst.
;Fügt e bei Index + 1 ein, wenn e größer/gleich ist als das Element in lst an der Stelle index.
;(oder wenn der Anfang von lst erreicht ist)
(define (insert-sort-element e lst)
  (define (insert-iter index)
    (cond
      [(< index 0) (cons e lst)]
      [(>= e (list-ref lst index)) (insert-at lst (+ index 1) e)]
      [else (insert-iter (- index 1))]))
  (insert-iter (- (length lst) 1)))



;Endrekursive Variante von Insertionsort in Racket
;Insertionsort Quelle: https://de.wikipedia.org/wiki/Insertionsort
(define (insert-sort lst)
  (foldl insert-sort-element '() lst))

;Usage:
(insert-sort '(2 4 1 6 4)) ;Output: '(1 2 4 4 6)
(insert-sort '()) ;Output: '()
```	

### Aufgabe 2.2
>Schreiben Sie eine Funktion flatten, die eine beliebig tief verschachtelte Listen-Struktur in eine Liste "glättet":
>```Scheme
>> (flatten '((a) b (c (d)  e) ()))
>'(a b c d e)
>```

```Scheme
#lang racket

(define (flatten lst)
  (if (null? lst)
      '()
      (if (list? (car lst))
          (append (flatten (car lst)) (flatten (cdr lst)))
          (cons (car lst) (flatten (cdr lst))))))

;Usage:
(flatten '((a) b (c (d)  e) ())) ;Output: '(a b c d e)
```	

### Aufgabe 2.3
>Beschreiben Sie den Unterschied zwischen der Nutzung von thunk und den Promises (delay und force). Wann ist das Ergebnis gleich und wann unterscheidet es sich? Für welchen Anwendungszweck würden Sie welche Version einsetzen?

Ein Thunk ist eine Funktion ohne Argumente, die einen Ausdruck umschließt bzw. kapselt.
Bei einem Thunk wird der gekapselte Ausdruck bei jedem Aufruf neu ausgewertet. Dies kann sinnvoll sein, für Funktionen mit Seiteneffekten oder bei Funktionen, die nicht immer die gleichen Resultate liefern (z.B. Uhrzeit in Sekunden).
Promises hingegen werden nur einmalig ausgewertet. Das Ergebnis wird dann zwischengespeichert und bei jedem weiteren Aufruf des Promises wird das zwischengespeicherte Ergebnis zurückgegeben.
Bei deterministischen Ausdrücken kann mit Promises, im Gegensatz zu Thunks, Rechenleistung gespart werden (z.B. eine bestimmte Anzahl an Nachkommastellen von Pi berechnen).

### Aufgabe 2.4
>Warum kann man delay nicht als Funktion schreiben?

Dies ist aufgrund von Rackets Evaluations Models und der Applicative Order nicht möglich.
Racket wertet bei Funktion alle Argumente aus, bevor die Funktion aufgerufen wird. Bei delay werden die Argumente nicht direkt ausgewertet.
Die Auswertung geschieht erst sobald sie mit force "erzwungen" wird. Hierfür wird der von delay erzeugte Promise der force Funktion als Argument übergeben.

### Aufgabe 2.5
>Implementierten Sie eine Funktion fib-stream, die einen Strom von zwei-elementigen Listen (n (fib(n))) erzeugt, wobei n die natürlichen Zahlen durchläuft. Die Funktion soll nicht jede Fibonacci-Zahl unabhängig berechnen.
>```Scheme
> (define a (fib-stream))
>> a
>((0 0) . #<promise>)
>> (tail a)
>((1 1) . #<promise>)
>> (tail (tail a))
>((2 1) . #<promise>)
>> (tail (tail (tail a)))
>((3 2) . #<promise>)
>> (tail (tail (tail (tail a))))
>((4 3) . #<promise>)
>> (tail (tail (tail (tail (tail a)))))
>((5 5) . #<promise>)
>```

```Scheme
#lang racket

;Get the n-th fib number
(define (fib n)
  (cond [(= n 0) 0]
        [(= n 1) 1]
        [else (+ (fib (- n 1)) (fib (- n 2)))]))

;Returns a list the index and the n-th fib number
(define (fib-with-index n)
  (list n (fib n)))

(define (fib-stream)
  (define (fib-stream-iter n)
    (cons (fib-with-index n)
          (delay (fib-stream-iter (+ n 1)))))
  (fib-stream-iter 0))

(define (tail s)
  (force (cdr s)))

(define a (fib-stream))
a                                    ;Output: ((0 0) . #<promise>)
(tail a)                             ;Output: ((1 1) . #<promise>)
(tail (tail a))                      ;Output: ((2 1) . #<promise>)
(tail (tail (tail a)))               ;Output: ((3 2) . #<promise>)
(tail (tail (tail (tail a))))        ;Output: ((4 3) . #<promise>)
(tail (tail (tail (tail (tail a))))) ;Output: ((5 5) . #<promise>)
```

### Aufgabe 2.6
>Gegeben folgende Racket-Code, der ein simples Bankkonto realisieren soll. Es soll möglich sein, ein neues Bankkonte einzurichten und Geld ein- und auszuzahlen.
>```Scheme
>(define (make-account money)
>  (lambda (movement)
>    (set! money (+ money movement))
>    money))
>
>(define a (make-account 10))
>(define b (make-account 100))
>```
>Und gegeben die folgende Interaktion mit der Racket-REPL:
>```Scheme
>> a
>#<procedure>
>> b
>#<procedure>
>> (a 10)
>20
>> (b 10)
>110
>> (b 10)
>120
>```
>Erläutern Sie anhand der Interaktion und des Programmcodes, wie in Racket Abschlussobjekte und zustandsorientierte Programmierung genutzt werden können, um Eigenschaften objektorientierter Programmierung umzusetzen. Sehen Sie Grenzen in der Umsetzung objektorientierter Programmierung in Racket? Wenn ja, welche und warum?

"Bei der Erzeugung von Funktionsobjekten merkt
sich das Objekt auch einen Verweis auf die
Definitionsumgebung. Dieses Paar aus
Lambda-Ausdruck und Umgebung nennen wir
Abschlussobjekt (closure)." [^3]

[^3]: "03-einfuehrung-drracket1.pdf" (Vorlesungsunterlagen) Folie 11.

Mit einem Abschluss-Objekt wird somit ein lokaler Speicherbereich gekapselt. Dies kann genutzt werden, um die Funktionalität von Klassen und Instanzen nachzuahmen.
Dieser Definitionsumgebung bleibt für die Lebensdauer des Abschluss-Objekts erhalten und kann von der Funktion verwendet werden.

Die Funktion "make-account" nimmt einen Anfangsbetrag als Argument und gibt ein neues Abschluss-Objekt zurück, 
welches als "movement"-Funktion verwendet werden kann, um Geld ein- oder auszuzahlen.
Dieses Abschluss-Objekt ahmt die Funktion einer "Bankkonto" Klasse, mit einer "money" Property/Variable nach.
Mit (a 0) kann nun der aktuelle Kontostand ausgegeben werden und mit (a x) (wenn x != 0) kann Geld ein/ausgezahlt werden.

Da der Money Wert in dem Abschluss-Objekt gekapselt ist, kann es mehrere Bankkonto "Instanzen" geben, die unabhängig voneinander funktionieren (ähnlich wie bei Instanzen einer Klasse).

Eine Grenze der Umsetzung objektorientierter Programmierung in Racket besteht darin, dass es keine Unterstützung für explizite Klassen und Vererbung gibt und dass die Kapselung von Daten und Funktionen durch Abschluss-Objekte nicht so leicht erkennbar ist wie in anderen objektorientierten Sprachen.
Abschluss-Objekte ermöglichen also eine Art von Zustandsmodellierung, sind aber kein vollständiges Substitut für objektorientierte Klassen.

### Aufgabe 2.7
Diese Aufgabe wurde zusammen mit Julian Glänzer gelöst.

>Implementieren Sie eine Funktion mk-mp3-control, die ein Objekt zurückliefert, das die Kontrolleinheit eines MP3-Spielers repräsentiert. (Es sollen aber nicht wirklich Dateien abgespielt werden.) Das Objekt soll folgende Informationen speichern/zurückliefern: Eine Liste, der gespeicherten MP3-Dateien (mit Dateiname und Dauer des Stücks), die Anzahl der Titel, der aktuelle Titel, der gerade abgespielt wird oder vorgewählt ist, den Abspielstatus, also ob derzeit ein Titel (welcher?) abgespielt wird oder stop, wenn kein Titel abgespielt wird. Folgende Botschaften soll das Objekt verstehen: laden: Hinzufügen einer neuen MP3-Datei an das Ende der Titelliste, loeschen: Löschen einer Datei aus der Liste, Übergabe der Nummer des zu löschenden Titels, abspielen/stop: Ändern des Abspielstatus, vor/zurück: Titel erhöhen oder erniedrigen, sind keine Titel vorhanden oder ist das Ende oder 1 erreicht, so wird die Nachricht ignoriert, unbekannte Nachrichten werden ignoriert.

```Scheme
#lang racket

; Als Vorlage diente die Funktion make-account aus "2022-11-16.rkt" (Vorlesungsunterlagen)

(define (mk-mp3-control)
  (define mp3-files '())
  (define selected-file-index 0)
  (define playing? #f)

  ;---------------------
  ;Folgende Botschaften soll das Objekt verstehen:
  ;---------------------

  ;Hinzufügen einer neuen MP3-Datei an das Ende der Titelliste
  (define (load song-name song-length)
    (set! mp3-files (append mp3-files (list (cons song-name song-length)))))

  ;loeschen: Löschen einer Datei aus der Liste (Übergabe der Nummer des zu löschenden Titels)
  (define (delete index)
    ;Set selected-file-index to 0 if the playing track is the one to be deleted
    (cond [(= index selected-file-index) (set! selected-file-index 0)])
    ;Delete track from mp3-files
    (set! mp3-files (remove (list-ref mp3-files index) mp3-files)))

  ;abspielen/stop: Ändern des Abspielstatus
  (define (toggle-play)
    (set! playing? (not playing?)))

  ;vor/zurück: Titel erhöhen oder erniedrigen,
  ;sind keine Titel vorhanden oder ist das Ende oder 1 erreicht, so wird die Nachricht ignoriert
  (define (next)
    (define next-index (+ selected-file-index 1))
    (cond [(> (length mp3-files) next-index) (set! selected-file-index next-index)]))

  (define (prev)
    (define prev-index (- selected-file-index 1))
    (cond [(>= prev-index 0) (set! selected-file-index prev-index)]))


  ;---------------------
  ;Das Objekt soll folgende Informationen speichern/zurückliefern:
  ;---------------------

  ;Eine Liste, der gespeicherten MP3-Dateien (mit Dateiname und Dauer des Stücks)
  (define (get-mp3-files) mp3-files)

  ;die Anzahl der Titel
  (define (get-file-count) (length mp3-files))

  ;der aktuelle Titel, der gerade abgespielt wird oder vorgewählt ist
  (define (get-selected-file)
    (cond [(= (length mp3-files) 0) null]
          [else (list-ref mp3-files selected-file-index)]))

  ;den Abspielstatus: also ob derzeit ein Titel (welcher?) abgespielt wird oder stop, wenn kein Titel abgespielt wird
  (define (play-state)
    (if playing?
        (get-selected-file)
        "stop"))

  (define (dispatch m)
    (cond
      ((equal? m 'load) load)
      ((equal? m 'delete) delete)
      ((equal? m 'toggle-play) toggle-play)
      ((equal? m 'next) next)
      ((equal? m 'prev) prev)
      ((equal? m 'get-mp3-files) get-mp3-files)
      ((equal? m 'get-file-count) get-file-count)
      ((equal? m 'get-selected-file) get-selected-file)
      ((equal? m 'play-state) play-state)))

  dispatch)

;Usage:
(define player (mk-mp3-control))

((player 'load) "Barrenkrieg" 197)
((player 'load) "Kaggduschne (Club Mix)" 107)
((player 'load) "Skrrr Skrrr" 126)

((player 'get-file-count))      ;Output: 3
((player 'delete) 0)
((player 'get-file-count))      ;Output: 2

((player 'play-state))          ;Output: "stop"
((player 'toggle-play))
((player 'play-state))          ;Output: '("Kaggduschne (Club Mix)" . 107)

((player 'next))
((player 'get-selected-file))   ;Output: '("Skrrr Skrrr" . 126)
((player 'prev))
((player 'get-selected-file))   ;Output: '("Kaggduschne (Club Mix)" . 107)
```	

### Aufgabe 2.8
>Gegeben sei die folgende Funktion in Typed Racket:
>```Scheme
>(: bar (-> (U Integer Boolean String) Integer))
>(define (bar x)
>(cond
>((number? x) (string-length (number->string x)))
>(else (string-length x))))
>```
>Wenn man diese Funktion versucht zu kompilieren, erhält man eine Fehlermeldung vom Type Checker. Erläutern Sie die Fehlermeldung und korrigieren Sie die Funktion.

`(: bar (-> (U Integer Boolean String) Integer))` gibt an, dass die Funktion einen Parameter vom Typ Integer, Boolean oder String erwartet.
In der Condition wird allerdings nur auf `(number?)` geprüft. Falls der Parameter nicht vom Typ Integer ist, könnte er vom Typ Boolean oder String sein.
In der folgenden korrigierten Version wird dies berücksichtigt:
```Scheme
#lang typed/racket
(: bar (-> (U Integer Boolean String) Integer))
(define (bar x)
  (cond
    [(number? x) (string-length (number->string x))]
    [(boolean? x) 1]
    [else (string-length x)]))
```

### Aufgabe 2.9
>Überführen Sie einige (4-5) Ihrer Programme aus den vorherigen Aufgaben in Typed Racket
```Scheme
#lang typed/racket

;Aufgabe Block 1 Aufgabe 6 in Typed Racket:
(: my-reverse (All (A) (Listof A) -> (Listof A)))
(define (my-reverse lst)
  (if (null? lst)
      '()
      (append (my-reverse (cdr lst)) (list (car lst)))))

;Usage
(my-reverse '(1 2 3))


;Aufgabe Block 1 Aufgabe 7 in Typed Racket:
(: my-find (All (A) (Listof A) A -> Boolean))
(define (my-find lst x)
  (if (null? lst)
      #f
      (if (equal? (car lst) x)
          #t
          (my-find (cdr lst) x))))

;Usage
(my-find '(1 44 123 187 3) 2)

;Aufgabe Block 1 Aufgabe 9 in in Typed Racket:
(: sum (-> (Listof Integer) Integer))
(define (sum l)
  (if (null? l)
      0
      (+ (car l) (sum (cdr l)))))

;Usage
(sum '(1 2 3))

;Aufgabe Block 1 Aufgabe 8 in Typed Racket:
(: square-list (-> (Listof Number) (Listof Number)))
(define (square-list lst)
  (map sqr lst))

;Usage
(square-list '(1 2 3 4 5))
```	

### Aufgabe 2.10
>Schreiben Sie ein map und ein foldr in Typed Racket.
```Scheme
#lang typed/racket

;map in typed racket mit Rekursion
(: my-map (All (T U) (-> (-> T U) (Listof T) (Listof U))))
(define (my-map func lst)
  (cond
    [(null? lst) '()]
    [else (cons (func (car lst)) (my-map func (cdr lst)))]))

;Usage
(my-map sqr '(1 2 3 4 5)) ;Output: '(1 4 9 16 25)

;foldr in typed racket mit Rekursion
(: my-foldr (All (T U) (-> (-> T U U) U (Listof T) U)))
(define (my-foldr func init lst)
  (cond
    [(null? lst) init]
    [else (func (car lst) (my-foldr func init (cdr lst)))]))

;Usage
(my-foldr - 0 '(1 2 3 4))
```

### Aufgabe 2.11
>Schreiben Sie ein Makro infix, mit dem Sie die Addition zweier Zahlen in Infix-Schreibweise errechnen lassen können. Anwendungsbeispiel: `(infix 1 + 2)`

```Scheme
#lang racket

;Allgemein
(define-syntax-rule (infix a sign b)
  (sign a b))

;Nur mit +
(define-syntax-rule (infix-plus a sign b)
  (cond [(equal? sign +) (sign a b)]
        [else (error "Only '+' is allowed")]))

;Usage:
(infix 1 + 2) ;Output: 3
(infix-plus 1 + 2) ;Output: 3
(infix-plus 1 - 2) ;Output: Only '+' is allowed
```

## Aufgabenblock 3
### Aufgabe 3.1
>Nachdem Sie nun schon etwas mit Racket gearbeitet haben, möchte ich Ihnen die Anregung und Gelegenheit geben, eine größere Programmieraufgabe mit Racket und der funktionalen Programmierung umzusetzen. Suchen Sie sich eine Aufgabe heraus wie beispielsweise eine Reihe von Algorithmen oder ein kleineres Spiel (Game of Life, ,Tic-Tac-Toe, Vier Gewinnt bspw. oder ein Kartenspiel), gerne auch eine betriebliche Aufgabe, die Sie schon in einer anderen Sprache bewältigt haben. Wenn Sie diese Aufgaben annehmen, dann senden Sie mir bitte vorher eine Nachricht mit Ihrem Plan, damit ich entscheiden kann, ob das realistisch ist und Ihnen ggf. Hinweise geben kann. Beschreiben Sie, an welcher Stelle Ihr Programmierstil funktional ist. Tip: Beginnen Sie mit dieser Aufgabe ca. zur Hälfte des Semesters.


Als größere Programmieraufgabe habe ich mich für eine einfache Implementierung von Minesweeper mit GUI entschieden. Hierfür verwende ich die `racket/gui/base` Bibliothek. Die Implementierung ist in der Datei `Minesweeper.rkt` zu finden, welche diesem Dokument beiliegt.

Zudem ist der Code auf Github zu finden: https://github.com/Kugelhaufen/Racket-Minesweeper

Ich habe für das Spielfeld mit der `button%` Klasse realisiert. Die Klasse wurde mit meiner Klasse `right-click-button%` erweitert um Rechtsklick für das Platzieren von Flaggen zu unterstützen.

Hierbei sei gesagt, dass sich die `canvas%` Klasse besser für solche Anwendungsfälle eignet und die `button%` eigentlich nicht unbedingt für Anwendungsfälle dieser Art gedacht wurde. Da es aber bereits eine Implementierung in `PLT Games` [^4] mit einem Canvas gibt, habe ich mich der Abwechslung halber für die Buttons entschieden.

[^4]: https://docs.racket-lang.org/games/mines.html (01.02.2023)

Hier ein paar Screenshots der Implementierung:

Im Spiel:

![Im Spiel](Minesweeper%20Screenshots/Game.jpg)

Verloren:

![Verloren](Minesweeper%20Screenshots/Lost.jpg)

Gewonnen:

![Gewonnen](Minesweeper%20Screenshots/Won.jpg)

> Beschreiben Sie, an welcher Stelle Ihr Programmierstil funktional ist.

Ich habe an einigen Stellen im Code Loops Verwendet.

Beispiel:

```Scheme
(define (generate-mine-field rows cols)
  (define mine-positions (take (shuffle (all-coordinates rows cols)) mine-count))
  (for/list ([row (in-range rows)])
    (for/list ([col (in-range cols)])
      ;Set value to "*" for mines or to the count of adjacent mines
      (if (member (cons row col) mine-positions)
          "💣"
          (length (set-intersect (adjacent-fields row col rows cols) mine-positions))))))
```

Loops sind nicht unbedingt funktional. Ich habe sie hier dennoch verwendet, da sie, für mich, in diesem Fall einfacher und lesbarer waren als Rekursion.

Funktionen wie `all-coordinates`, `adjacent-fields`, `generate-mine-field` sind eher funktional, da sie keine Seiteneffekte haben und immer das gleiche Ergebnis zurückgeben. Diese Funktionen existieren getrennt von den Funktionen mit Seiteneffekten.

```Scheme	
(define (all-coordinates rows cols)
  (for*/list ([i (in-range rows)]
              [j (in-range cols)])
    (cons i j)))

(define (cord-in-bounds row col rows cols)
  (and (>= row 0) (>= col 0) (< row rows) (< col cols)))

(define (adjacent-fields row col rows cols)
  (filter
   (lambda (x) (cord-in-bounds (car x) (cdr x) rows cols))
   (list (cons (- row 1) (- col 1))
         (cons (- row 1) col)
         (cons (- row 1) (+ col 1))
         (cons row (- col 1))
         (cons row (+ col 1))
         (cons (+ row 1) (- col 1))
         (cons (+ row 1) col)
         (cons (+ row 1) (+ col 1)))))
```
An diversen Stellen wurden Lambda Ausdrücke verwenden, was ebenfalls funktional ist.
So zum Beispiel auch um die "Button Clicks" zu behandeln:

```Scheme
(new right-click-button%
               [parent sub-panel]
               [min-width 65]
               [min-height 65]
               [stretchable-width #f]
               [stretchable-height #f]
               [vert-margin 0]
               [horiz-margin 0]
               [font (make-object font% 25 'default 'normal  'ultraheavy)]
               [label ""]
               [is-clickable-func (lambda () (not game-over))]
               [right-click-callback (lambda (b)
                                       (cond [(equal? "🚩" (send b get-label))
                                              (send b set-label "")]
                                             [(equal? "" (send b get-label))
                                              (send b set-label "🚩")]))]
               [callback (lambda (b e)
                           (cond [(equal? "" (send b get-label)) (try-clear-field i j)]))])
```

### Aufgabe 3.2
>Wie wird in Racket/Scheme lexical scope umgesetzt und warum ist dynamic scope eine schlechte Idee? Illustrieren Sie Ihr Argument an einem selbst entwickelten Beispiel.

"Lexical scope" und "Dynamic Scope" sind zwei unterschiedliche Methoden zur Bestimmung des Geltungsbereichs einer Variablen. Beim Lexical Scope wird dies durch die Position der Deklaration innerhalb des Quellcodes bestimmt. Das bedeutet, dass auf eine Variable nur innerhalb desselben Codeblocks (z.B. eine Funktion) zugegriffen werden kann, in dem sie deklariert wurde. Beim Dynamic Scope hingegen wird der Geltungsbereich durch die Reihenfolge der Funktionsaufrufe bzw. der Deklarationen und nicht durch die Position bestimmt. 
Dynamic Scope ist eine schlechte Idee, da es zu unerwarteten Ergebnissen führen kann und schwerer zu debuggen ist. Ein Beispiel dafür ist folgendes:
    
```Scheme
#lang racket

(define text "Hello World!")

(define (print-number num)
  (define text (number->string num))
  (display text))

(print-number 123) ;Output: 123

;Das hätte mit Dynamic Scoping nicht funktioniert,
;da "text" bereits definiert ist und = "Hello World!" ist.
```

### Aufgabe 3.3
>Nehmen Sie zum Zitat "Objects are a poor man’s closures. Closures are a poor man’s objects." Stellung und vergleichen die vorgestellten Möglichkeiten der Objektorientierung mit einer Ihnen bekannten objektorientierten Programmiersprache.

Das Zitat zeigt auf, dass Objekte und Closures ähnliche Funktionen erfüllen, aber unterschiedlich implementiert werden. Objekte in objektorientierten Sprachen dienen dazu, Daten und Verhaltensweisen zu organisieren und zu kapseln. Closures hingegen sind ein Konzept der funktionalen Programmierung, bei dem eine Funktion zusammen mit ihren umgebenden Variablen zusammengefasst werden, um eine Art von "gekapseltem" Verhalten zu ermöglichen.
Die meisten modernen Sprachen unterstützen mehrere Paradigmen. In C# wird, zum Beispiel, wird hauptsächlich objektorientiert mit Klassen programmiert- C# unterstützt aber auch funktionale Konzepte. So können beispielsweise Closures mithilfe von Lambda Ausdrücke erstellt werden.

### Aufgabe 3.4
>Nennen und erläutern Sie mindestens drei Gründe, warum es sinnvoll sein kann, ein Typsystem zu verwenden. Erläutern Sie anschließend den Unterschied zwischen einer dynamischen Typprüfung und einer statischen Typprüfung. Sie können für Ihre Antwort neben Racket auch noch auf andere Ihnen bekannte Programmiersprachen zurückgreifen.

1. Fehlervermeidung: Ein Typsystem kann zu Fehlervermeidung und besserer Sicherheit beitrage, da verhindert wird, dass keine ungültige Daten verarbeitet werden. Es stellt sicher, dass jede Variablen oder Funktionen den erwarteten Typ hat und so keine unerwarteten Ergebnisse auftreten.
2. Code-Verständlichkeit: Durch die Angabe der Typen kann der Entwickler schnell erkennen, welche Art von Daten erwartet werden und wie sie verarbeitet werden. Das macht es einfacher, den Code zu verstehen und zu warten.
3. Optimierungen: Ein Typsystem kann es dem Compiler ermöglichen, den Code besser zu optimieren. Wenn der Compiler weiß, welche Art von Daten erwartet wird, kann er den Code entsprechend optimieren.

Dynamische Typprüfung werden die Typen erst zur Laufzeit geprüft. Ein Beispiel hierfür ist Python. Ein Vorteil ist beispielsweise, dass dynamische Typprüfung mehr Flexibilität ermöglicht.
Bei Statischer Typprüfung werden die Typen bereits während der Kompilierung geprüft. Ein Beispiel hierfür ist C#. Vorteilhaft ist hier vor allem die erhöhte Sicherheit, da Fehler bereits während der Entwicklungszeit erkannt werden können. Andernfalls besteht das Risiko, dass Fehler beim Testen nicht gefunden werden und ausgeliefert werden.

### Aufgabe 3.5
>Beschreiben Sie, was man im Lambda-Kalkül unter einer Abstraktion und einer Applikation versteht und geben hierfür jeweils ein Beispiel an.

Unter einer Abstraktion im Lambda-Kalkül versteht man die Möglichkeit Funktionen mit abstrakten Parametern zu definieren. Mit "λ" wird also eine Funktion definiert, die einen Parameter nimmt und einen (mathematischen) Term zurückgibt.

Beispiel:

    λx[x + 5 - 2*x] 

addiert 5 zu einer Zahl x und subtrahiert 2 mal die Zahl x
Das Einsetzen von Konkreten Werten in die Parametern (bzw. das Ersetzten der Parameter durch die Werte) wird als Applikation (oder Anwendung) des Ausdrucks bezeichnet. Zentrale Idee ist hierbei die sogenannte β-Reduktion.

Beispiel:

    (2 in obigen Ausdruck einsetzten)
    (λx[x + 5 - 2*x])2 -> 2 + 5 - 2*2 
    = 2 + 5 - 2*2
    = 3

### Aufgabe 3.6
>Erklären Sie den Unterschied zwischen der Reduktionsstrategie normal order und applicative order. Führen beide immer zum gleichen Ergebnis? Begründen Sie Ihre Antwort an einem selbst erstellten Beispiel.

Applicative Order werden alle Argumente einer Funktion ausgewertet, bevor die Funktion aufgerufen wird. Bei der normal Order werden die Argumente erst dann ausgewertet, wenn sie benötigt werden.
Die Beiden Reduktionsstrategien führen nicht immer zum gleichen Ergebnis. Ein Beispiel hierfür gab es bereits in der Aufgabe 4 aus Block. "new-if" führt nicht zum gewünschten Verhalten, da alle Argumente ausgewertet werden, bevor "new-if" aufgerufen wird.
Ein weiteres Beispiel war in der Aufgabe 3 aus Block 1 zu finden. Das Programm gerät bei Applicative Order in eine Endlosschleife.
Konkret kann man die beiden Reduktionsstrategien an folgendem Beispiel erkennen:

```Scheme
#lang racket

(define (new-add x y)
  (begin
    (displayln "adding...")
    (+ x y)))

(define (new-display x)
  (begin
    (displayln "displaying...")
    (displayln x)))

(new-display (new-add 1 3))

;Output bei applicative order (Standard in Racket):
;adding...
;displaying...
;4

;Output bei normal order:
;displaying...
;adding...
;4
```

## Aufgaben zur Reflektion

### Aufgabe R.1
>Was ist Ihr erster Gedanke gewesen, als Sie die Syntax von Racket gesehen haben? Wie sehr beeinflusst die Syntax einer Programmiersprache Ihr Lernen? Ist es hinderlich, dass Sie sich an eine neue Syntax gewöhnen müssen oder fällt es Ihnen leichter, neue Konzepte mit einer ungewohnten Syntax zu erlenen?

Mein erster Eindruck war nicht gut und meine Meinung über die Syntax blieb auch in den folgenden Wochen gleich.
Vor allem die vielen Klammern, die sich schon bei vergleichsweise einfachen Funktionen stark ansammeln, machen die Sprache meiner Meinung nach nur schwer lesbar.
Racket Code meistens von starker Verschachtelung und stark "komprimierten" Funktionen geprägt.
Ich finde, dass die gute Lesbarkeit von Programmcode sehr wichtig ist. Die Racket Syntax und Konventionen halte Ich für eher suboptimal im Punkt Lesbarkeit.
Dementsprechend bevorzuge ich die Syntax und die Konventionen von imperativen Sprachen wie C#.


Ich denke nicht, dass die neue Syntax einen positiven Einfluss auf das Lernen und Verstehen der Konzepte hatte. 
Ein besonders großes Hinderndes war es aber auch nicht.


### Aufgabe R.2
>Haben Sie bereits mit einer funktionalen Programmiersprache Kontakt gehabt? Privat, im Betrieb? Wenn ja, welche war es? Wie hat diese Sprache die vorgestellten Konzepte (statische vs. dynamische Bindung, eager vs. lazy evaluation, strikt vs. nicht strikt) umgesetzt?

Nein, nicht direkt. Jedoch unterstützen viele moderne Programmiersprachen verschiedene Paradigmen. 
Dadurch bin ich bereits mit einigen funktionalen Konzepten, wie z.B. Lambda Ausdrücken in C#, in Kontakt gekommen.

### Aufgabe R.3
>Auf welche Schwierigkeiten (wenn überhaupt) stoßen Sie beim Erlernen des Programmierparadigmas der funktionalen Programmierung? Worin sehen Sie die Ursachen?

Es gab keine nennenswerten Schwierigkeiten beim Erlernen des Programmierparadigmas.

### Aufgabe R.4
>Sehen Sie in Typed Racket Vorteile oder Nachteile gegenüber dem "normalen" Racket? Wenn ja, welche?

Ich bin (statischen) Typsystem sehr positiv gegenüber eingestellt. 
Auch im Falle von Typed Racket hilft das Typsystem, meiner Meinung nach, Fehler vorzeitig zu vermeiden und die Verständlichkeit vom Code zu erhöhen.
Jedoch finde ich, wie auch im normalen Racket, die Syntax nicht besonders ansprechend. 
Zudem ist Typed Racket ein Unterprojekt von Racket. Dadurch wird eine simple Integration mit anderen Racket-Bibliotheken ermöglicht, was die Flexibilität erhöht.
Ein Nachteil ist, dass der Code größer/länger wird und die Entwicklungszeit länger wird.

### Aufgabe R.5
>Vergleichen Sie das Typsystem von Typed Racket mit einem anderen Ihnen bekannten Typsystem. Welche Stärken und Schwächen der beiden Systeme können Sie ausmachen?

Verglichen mit dem Typsystem von C# sehe ich vor allem relevante Unterschiede im Bereich Syntax.
Die Typed Racket Syntax ist meiner Meinung nach schlechter lesbar als die von C#.
Andererseits bietet die Typed Racket Syntax Flexibilität, da sie Standard Racket sehr ähnlich ist. Es ist einfach Typed Racket in Racket umzuschreiben.

### Aufgabe R.6
>Halten Sie Programmiersprachen mit dynamischer oder statischer Typprüfung für "besser"? Für welchen Einsatzzweck würden Sie die eine oder andere Typprüfung empfehlen?

Dynamische Typprüfung eignet sich vor allem für das schnelle Iterieren und das schnelle Erstellen von Code.
Für größere Projekte, bei denen hohe Qualität und Fehlersicherheit wichtig ist, halte ich statische Typsystem für am sinnvollsten.
Den Mehraufwand durch statische Typprüfung halte ich persönlich für nicht für besonders groß, weshalb ich statische Typprüfung allgemein bevorzuge.

### Aufgabe R.7
>Zum Ende des Semesters:
>* Welche Inhalte haben Sie besonders interessiert?
>* Welche Inhalte haben Einfluss auf Ihr zukünftiges Programmieren?
>* Ich hatte Ihnen am Anfang des Semesters das Zitat von Alan Perlis präsentiert: "A language that doesn’t affect the way you think about programming is not worth knowing." Hat das Modul und Racket Ihre Art über Programmieren zu denken verändert? Wenn ja, in welcher Weise? Wenn nein, warum denken Sie, dass es das nicht getan hat?

"Welche Inhalte haben Sie besonders interessiert?"
Ich finde die allgemeinen funktionalen Konzepte interessant. 
Racket als Sprache fand ich jedoch nicht besonders spannend, unter anderem weil die Sprache eher unbekannt ist und sehr selten verwendet wird.

"Welche Inhalte haben Einfluss auf Ihr zukünftiges Programmieren?"
Das verbesserte Verständnis von funktionalen Konzepten wird bei mir in zukünftige Programmiertätigkeiten im Hinterkopf bleiben und in die Arbeit einfließen.
Ganz konkret war mir zudem das Konzept der Endrekursion nicht bekannt.
Insbesondere wegen der besseren Verwendung von Speicherplatz finde ich Endrekursion wichtig.

"A language that doesn’t affect the way you think about programming is not worth knowing."
Viele der aktuell beliebten Programmiersprachen ähneln sich stark (die meisten stammen von der C Familie ab). Oft wird eine Programmiersprache nur aufgrund der zur Verfügung stehenden Tools und Frameworks ausgewählt.
Es ist, meiner Meinung nach, keine besonders große "geistige Bereicherung" sehr viele Sprachen zu lernen die sich in grundlegenden Konzepten nicht unterscheiden.
Sprachen mit grundlegend anderen Paradigmen, können den eigenen Horizont erweitern. Ich halte das Zitat daher für sinnig. Wie im letzten Absatz bereits beschrieben, sind diverse Eindrücke aus dem Modul in meine Denkweise eingeflossen.