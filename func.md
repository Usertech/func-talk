title: Funkcionální programování
author:
  name: Lukáš Rychtecký
  twitter: lukasrychtecky
controls: true
style: style.css

--

# Funkcionální programování
## Lukáš Rychtecký
## [https://twitter.com/LukasRychtecky](https://twitter.com/LukasRychtecky)

--

### Outline

* Motivace
* Co je to funkcionální programování?
* Funcionální programování v praxi
* Předsudky
* Jak na side efekty?
* Příklady v ClojureScriptu

--

### Motivace

Jak otestujeme?

```python
def add(a: int, b: int) -> int:
    return a + b
```

--

### Motivace

Jak otestujeme?

```python
def add(a: int, b: int) -> int
    return a + b
```

Snadno

```python
assert 3 add(1, 2)
```

--

### Motivace

Jak otestujeme?

```python
def add(a: int, b: int) -> int:
    return a + b
```

Snadno

```python
assert 3 add(1, 2)
```

Proč?

--

### Motivace

Jak otestujeme?

```python
def add(a: int, b: int) -> int:
    return a + b
```

Snadno

```python
assert 3 add(1, 2)
```

Proč?

Protože se jedná o `pure function`

Tzn. funkce, která pracuje pouze s argumenty a vrací výsledek

--

### Motivace

`Pure function` je tedy taková funkce, která:

* pracuje s argumenty
* vrací deterministický výsledek
* nemodifikuje ostatní proměnné -> proměnné jsou immutabilní == konstanty

--

### Motivace - pure functions

Python
```python
def add(a: int, b: int) -> int:
    return a + b
```

Clojure
```clojure
(defn add [a b]
    a + b)
```

C
```c
int add(int a, int b) {
    return a + b
}
```

Je jazyk C funkcionální?

--

### Motivace - Co jsou to side effects?


* modifikace proměnné z okolí funkce
* IO operace (práce s diskem, DB, HTTP, apod.)

Příklad:
```python
for a in [1, 2, 3, 4]:
    print(a)
```

Jak to otestujeme?

--

### Motivace - OOP je přeci nejlepší!

Problémy OOP:

* nested hierarchical classes
* vede ke zbytečně složitému API
* side effects and mutability


Jaká je logika tohoto API?
```javascript
var reader = FileReader();
reader.onload = function(event) {
    // do stuff
};
reader.readAsDataURL(file);
```


--

### Funcionální programování v praxi

* kořeny v roce 1930
* jiný styl přemýšlení nad problémem -> transformace dat

#### Pilíře funkcionálního programování

* first-class and higher-order functions
* pure functions
* lazy evaluation
* deklarativní styl programování

--

### First-class and higher-order functions

* funkci lze předat jako parametr jiné funkci
* funkce může vracet jinou funkci

```python
def foo(func):
    return func(1, 3)

def bar(a, b):
    # do stuff

foo(bar)
```

--

### Pure functions

* funkce nemá žádné side effects (IO operace apod.)

```python
def count_items(result):
    return len(result.items)

map(count_items, results)  # [1, 2, 4, 1]
```

--

### Lazy evaluation

* výrazy a iterace se provedou až ve chvíli, kdy jsou potřeba
* snadno lze pracovat s nekonečnou sekvencí

```clojure

(take 3 infinity-iter) ; [1 4 4]

(take 2 infinity-iter) ; [1 5]
```

--

### Deklarativní vs. imperativní styl programování

Imperativní říká počítači **jak** má program fungovat (alokuj paměť ...)

Deklarativní říká počítači **co** má být výsledkem (jako SQL, dej mi tyto data)

--

### Deklarativní vs. imperativní styl programování - příklad

Imperativní

```python
result = []  # vytvoř instanci seznamu
for item in items: # iteruj na prvky
    if item > 50: # pokud je prvek větší než 50
        result.append(item) # vlož jej do seznamu
```

Deklarativní

```python
filter(lambda item: item > 50, items) # vyber prvky větší než 50
```

nebo pomocí list comprehension

```python
[item for item in items if item > 50]
```

--

### Reduce - příklad

```clojure
(def form
  {:first-name ""
   :last-name "Doe"
   :phone ""})


(defn validate
  [value]
  (if (string/blank? value)
    [value "Cannot be empty!"]
    [value nil]))


(defn validate-form
  [result field value]
  (let [validity (validate value)]
    (merge result {field (validate value)})))

(reduce-kv validate-form {} form)

; {:first-name ["" "Cannot be empty!"]
;  :last-name ["Doe" nil]
;  :phone ["" "Cannot be empty!"]}
```

--

### Předsudky

#### Když je všechno immutabilní, tak mi musí dojít brzy paměť!

To není pravda. Datové struktury jsou implementované jako Persistent vector. Více třeba na
 [http://blog.higher-order.net/2009/02/01/understanding-clojures-persistentvector-implementation](http://blog.higher-order.net/2009/02/01/understanding-clojures-persistentvector-implementation)


--

### Předsudky

#### Nekonečná sekvence se přeci do paměti nevejde!

To je sice pravda, ale protože je vyhodnocování lazy, načítá se opravdu pouze to, s čím se pracuje.
Tzn. skrze celou aplikaci si mohu předávat iterátor nad milionem položek, ale v paměti je jen uložená pozice iterátoru.

--

### Nekonečná sekvence - příklad

Např.:

```clojure
(filter even? (range)) ; neudělá nic

(next (filter even? (range))) ; vrátí pouze 2 a dál nic nepočítá
```

Více třeba na [http://clojure-doc.org/articles/language/laziness.html](http://clojure-doc.org/articles/language/laziness.html)

--

### Jak na side efekty?

* minimalizovat je, mít co nejvíce pure functions
* pro pure functional jazyky - monády [https://wiki.haskell.org/Monad](https://wiki.haskell.org/Monad)

--

### ClojureScript

* funkcionální jazyk
* immutabilní struktury
* dialekt Lispu (počátky 1958)
* všechno je vektor
* kompilace do JavaScriptu

--

![LISP](https://pbs.twimg.com/media/CbqjZJwWEAAkvH3.jpg)

--

### ClojureScript

#### Výhody

* stabilní syntaxe
* immutabilita
* datové struktury
* makra
* malá, ale kvalitní komunita
* přenositelnost kódu mezi Clojure a ClojureScriptem (není 100%)

--

### ClojureScript


#### Nevýhody

* jiné "přemýšlení"
* nekomfortní programování mutací DOM
* kompilátor běží v Javě, takže si vezme trochu více paměti

--

### ClojureScript vs. JavaScript

JavaScript
```javascript
foo(1, 2, 3)
```

ClojureScript
```clojure
(foo 1 2 3)
```

--

### ClojureScript - příklady

```clojure
(println "Hello world!")

(defn square [x]
  (* x x))

(map square (range 10))

(->> orders
     (map #(count (% :unpaid_complete_orders)))
     (reduce + 0))
```

--

### ClojureScript + ReactJS = Reagent

* [Reagent](https://reagent-project.github.io/) - wrapper nad ReactJS, knihovna pro tvorbu UI
* [Re-frame](https://github.com/Day8/re-frame) - "MVC" framework pro UI web. aplikace

#### Flow

```text
View -> dispatch -> Handler -> Subscriber
|                                        |
     <-                      <-
```

--

### Reagent + Re-frame: views

* definuje UI

Pure view

```clojure
(defn hello-view
  [name]
  [:h1 "Hello " name])
```

Tohle už není pure view

```clojure
(defn hello-view
  []
  (let [name (subscribe [:name])]
    (fn []
      [:h1 "Hello " @name])))
```

--

### Reagent + Re-frame: handlers

Handler + side effect

* "klasický" handler na události

```clojure
(register-handler
  (after write-to-log) ; <-- side effect, zůstává mimo hlavní logiku
  :set-name
  (fn [db [_ name]]
    (assoc db :name name)))
```

--

### Reagent + Re-frame: subscribers

Subscriber

* pohled na data

```clojure
(register-sub
  :name
  (fn [db _]
    (reaction (get @db :name))))
```

--

### Thanks for your attention

* [http://www.braveclojure.com/](http://www.braveclojure.com/)
* [https://clojuredocs.org/](https://clojuredocs.org/)
* [https://github.com/clojure/clojurescript](https://github.com/clojure/clojurescript)
* [http://cljs.info/cheatsheet/](http://cljs.info/cheatsheet/)
* [https://github.com/Day8/re-frame](https://github.com/Day8/re-frame)
* [https://reagent-project.github.io/](https://reagent-project.github.io/)
* [http://www.learnpython.org/](http://www.learnpython.org/)
