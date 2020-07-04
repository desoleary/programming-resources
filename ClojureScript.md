## Development
- [Wiki](https://github.com/clojure/clojurescript/wiki)
- [ClojureScript Online Console](http://www.clojurescript.net/)
- [ClojureScript Docs](http://clojuredocs.org/)
- [Style Guide](http://clojuredocs.org/examples-styleguide)
- [Awesome ClojureScript GitHub](https://github.com/hantuzun/awesome-clojurescript)
- [Awesome ClojureScript GitHub II](https://github.com/Jarzka/awesome-clojurescript)
- [Top 90 ClojureScript Projects](https://awesomeopensource.com/projects/clojurescript)

#### Project Setup

```shell script
$ wget https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein
$ mv lein /usr/local/bin
$ exit
```

#### Tools
[Compiles ClojureScript](https://github.com/thheller/shadow-cljs)

## Tutorials 
- [ClojureScript Koans - Interactive Exercises](http://clojurescriptkoans.com/)

## Resources
- [Online Console](https://repl.it/languages/clojure)
- [Videos](https://changelog.com/posts/rich-hickeys-greatest-hits)
- [Book - Clojure for the Brave and True](https://www.braveclojure.com/clojure-for-the-brave-and-true/)
- [Clojure from the ground up](https://aphyr.com/posts/301-clojure-from-the-ground-up-welcome)
- [Clojure Problems](http://www.4clojure.com/problems)

#### React
[Simple ClojureScript interface to React](https://github.com/reagent-project/reagent)

#### ClojureScript vs JavaScript

- [Differences between ClojureScript and JS via Examples](https://www.freecodecamp.org/news/here-is-a-quick-overview-of-the-similarities-and-differences-between-clojurescript-and-javascript-c5bd51c5c007/)
- [JavaScript Examples](https://gist.github.com/jacekschae/17992f9627f3e247ba46f62fa35fdaec)
- [ClojureScript Examples](https://gist.github.com/jacekschae/ddffcdcd981ecf80dbe66fbef8b54719)

#### Cheat Sheets
[Official Cheat Sheet](https://cljs.info/cheatsheet/)
[Cheat Sheet](https://github.com/readevalprintlove/clojurescript-cheatsheet/blob/master/cljs-cheatsheet.pdf)

#### Promises / Async
- [Code Examples](https://github.com/clojure/core.async/blob/master/examples/walkthrough.clj)
- [Promises (JavaScript vs ClojureScript)](https://clojurescript.org/guides/promise-interop)

## Installation (OSX)

```shell script
$ brew install clojure/tools/clojure
```

## Building
[Webpack Setup Guide](https://clojurescript.org/guides/webpack)

```shell script
$ yarn add --dev webpack webpack-cli
$ yarn add react react-dom
$clj -m cljs.main -co build.edn -v -c -r
```

**build.edn**
```clojure
{:main hello-bundler.core
 :output-to "out/index.js"
 :output-dir "out"
 :target :bundle
 :bundle-cmd {:none ["npx" "webpack" "out/index.js" "-o" "out/main.js" "--mode=development"]
              :default ["npx" "webpack" "out/index.js" "-o" "out/main.js"]}
 :closure-defines {cljs.core/*global* "window"}}
```
**Build command**

`$ clj -m cljs.main -co build.edn -v -c -r`
`clj -m cljs.main -co build.edn -O advanced -v -c -s` Advance build enabled
- Opens browser http://localhost:9000
- You can run commands from clj RELP E.g. `(js/alert "Hello CLJS!")`

## OM Framework (Application State)
- [API Documentation](https://github.com/omcljs/om/wiki/Documentation)
- [Guides](https://github.com/omcljs/om/wiki#om-next)
- [Documentation](https://github.com/omcljs/om/wiki#om-next)
- [Tutorial](https://github.com/omcljs/om/wiki/Quick-Start-%28om.next%29)
- [Basic Tutorial](https://github.com/omcljs/om/wiki/Basic-Tutorial)

```clojure
(defonce app-state
  (atom
    {:contacts
     [{:first "Ben" :last "Bitdiddle" :email "benb@mit.edu"}
      {:first "Alyssa" :middle-initial "P" :last "Hacker" :email "aphacker@mit.edu"}
      {:first "Eva" :middle "Lu" :last "Ator" :email "eval@mit.edu"}
      {:first "Louis" :last "Reasoner" :email "prolog@mit.edu"}
      {:first "Cy" :middle-initial "D" :last "Effect" :email "bugs@mit.edu"}
      {:first "Lem" :middle-initial "E" :last "Tweakit" :email "morebugs@mit.edu"}]}))

(defn contacts-view [data owner]
  (reify
    om/IRender
    (render [this]
      (dom/div nil
        (dom/h2 nil "Contact list")
        (apply dom/ul nil
          (om/build-all contact-view (:contacts data)))))))
```

#### Syntax

[Syntax Examples](https://www.braveclojure.com/core-functions-in-depth/)
[Core Functions in Detail](https://www.braveclojure.com/core-functions-in-depth/)
[Functional Programming Overview](https://www.braveclojure.com/functional-programming/)
[Organizing your project](https://www.braveclojure.com/organization/)
[Clojure Alchemy: Reading, Evaluation, and Macros](https://www.braveclojure.com/read-and-eval/)
[Writing Macros](https://www.braveclojure.com/writing-macros/)
[The Sacred Art of Concurrent and Parallel Programming](https://www.braveclojure.com/concurrency/)
[Clojure Metaphysics: Atoms, Refs, Vars, and Cuddle Zombies](https://www.braveclojure.com/zombie-metaphysics/)
[Mastering Concurrent Processes with core.async](https://www.braveclojure.com/core-async/)
[Working with the JVM](https://www.braveclojure.com/java/)
[Creating and Extending Abstractions with Multimethods, Protocols, and Records](https://www.braveclojure.com/multimethods-records-protocols/)
[Building and Developing with Leiningen](https://www.braveclojure.com/appendix-a/)
[Afterword](https://www.braveclojure.com/afterword/`)
```shell script
(operator operand1 operand2 ... operandn)

(+ 1 2 3)
; => 6
```

#### Control Flow

```clojure
(if true
  "By Zeus's hammer!"
  "By Aquaman's trident!")
; => "By Zeus's hammer!"

(if true
  (do (println "Success!")
      "By Zeus's hammer!")
  (do (println "Failure!")
      "By Aquaman's trident!"))
; => Success!
; => "By Zeus's hammer!"

(when true
  (println "Success!")
  "abra cadabra")
; => Success!
; => "abra cadabra"
```

# Defining variables
```clojure
(def failed-protagonist-names
  ["Larry Potter" "Doreen the Explorer" "The Incredible Bulk"])

(def severity :mild)
(def error-message "OH GOD! IT'S A DISASTER! WE'RE ")
(if (= severity :mild)
  (def error-message (str error-message "MILDLY INCONVENIENCED!"))
  (def error-message (str error-message "DOOOOOOOMED!")))
```

#### Data Structures

```clojure
failed_protagonist_names = [
  "Larry Potter",
  "Doreen the Explorer",
  "The Incredible Bulk"
]
```

#### Numbers
```clojure
93
1.2
1/5
```

#### Strings

```clojure
"Lord Voldemort"
"\"He who must not be named\""
"\"Great cow of Moscow!\" - Hermes Conrad"
```

#### Maps

```clojure
{:first-name "Charlie" :last-name "McFishwich"}

{:name {:first "John" :middle "Jacob" :last "Jingleheimerschmidt"}}
```

###### Hash Map

```clojure
(hash-map :a 1 :b 2)

(get {:a 0 :b 1} :b)

(get-in {:a 0 :b {:c "ho hum"}} [:b :c])

(:d {:a 1 :b 2 :c 3} "No gnome knows homes like Noah knows")
; Setting default value
```

#### Keywords

```clojure
(:a {:a 1 :b 2 :c 3}) 
; => 1

(get {:a 1 :b 2 :c 3} :a)
; => 1
```

#### Vectors

```clojure
[3 2 1]

(vector "creepy" "full" "moon")
; => ["creepy" "full" "moon"]

(get [3 2 1] 0)
; => 3

(get ["a" {:name "Pugsley Winterbottom"} "c"] 1)
; => {:name "Pugsley Winterbottom"}

(conj [1 2 3] 4)
; => [1 2 3 4]
; Add element to vector
```

#### Lists

```clojure
'(1 2 3 4)
; => (1 2 3 4)

(nth '(:a :b :c) 0)
; => :a
; not as quick as using get

(conj '(1 2 3) 4)
; => (4 1 2 3)
; elements added to the beginning of a list
```

#### Sets 
> Collection of unique values

```clojure
#{"kurt vonnegut" 20 :icicle}

(hash-set 1 1 2 2)
; => #{1 2}

(conj #{:a :b} :b)
; => #{:a :b}
; Attempts to add b to set

(contains? #{:a :b} :a)
; => true

(:a #{:a :b})
; => :a
; using a keyword to access key value

(get #{:a :b} :a)
; => :a
```

#### Functions

```clojure
(+ 1 2 3 4)
(* 1 2 3 4)
(first [1 2 3 4])

(or + -)
; => #<core$_PLUS_ clojure.core$_PLUS_@76dace31>
; returns the second operator

(map inc [0 1 2 3])
; => (1 2 3 4)
; increment by one

(+ (inc 199) (/ 100 (- 7 2)))
(+ 200 (/ 100 (- 7 2))) ; evaluated "(inc 199)"
(+ 200 (/ 100 5)) ; evaluated (- 7 2)
(+ 200 20) ; evaluated (/ 100 5)
220 ; final evaluation

(if good-mood
  (tweet walking-on-sunshine-lyrics)
  (tweet mopey-country-song-lyrics))
```

###### Defining functions

```clojure
➊ (defn too-enthusiastic
➋   "Return a cheer that might be a bit too enthusiastic"
➌   [name]
➍   (str "OH. MY. GOD! " name " YOU ARE MOST DEFINITELY LIKE THE BEST "
  "MAN SLASH WOMAN EVER I LOVE YOU AND WE SHOULD RUN AWAY SOMEWHERE"))

(too-enthusiastic "Zelda")
; => "OH. MY. GOD! Zelda YOU ARE MOST DEFINITELY LIKE THE BEST MAN SLASH WOMAN EVER I LOVE YOU AND WE SHOULD RUN AWAY SOMEWHERE"

; At ➊, too-enthusiastic is the name of the function, and it’s followed by a descriptive docstring at 
; ➋. The parameter, name, is given at ➌, and the function body at 
; ➍ takes the parameter and does what 
; it says on the tin—returns a cheer that might be a bit too enthusiastic.

(defn no-params
  []
  "I take no parameters!")
(defn one-param
  [x]
  (str "I take one parameter: " x))
(defn two-params
  [x y]
  (str "Two parameters! That's nothing! Pah! I will smoosh them "
  "together to spite you! " x y))
; Parameters and Arity

(defn multi-arity
  ;; 3-arity arguments and body
  ([first-arg second-arg third-arg]
     (do-things first-arg second-arg third-arg))
  ;; 2-arity arguments and body
  ([first-arg second-arg]
     (do-things first-arg second-arg))
  ;; 1-arity arguments and body
  ([first-arg]
     (do-things first-arg)))

(defn x-chop
  "Describe the kind of chop you're inflicting on someone"
  ([name chop-type]
     (str "I " chop-type " chop " name "! Take that!"))
  ([name]
     (x-chop name "karate")))
; Arity overloading is one way to provide default values for arguments. In the following example, "karate" is the default argument for the chop-type parameter:

(x-chop "Kanye East")
; => "I karate chop Kanye East! Take that!"
; karate is set as a default value

(defn codger-communication
  [whippersnapper]
  (str "Get off my lawn, " whippersnapper "!!!"))

(defn codger
  [& whippersnappers]
  (map codger-communication whippersnappers))

(codger "Billy" "Anne-Marie" "The Incredible Bulk")
; => ("Get off my lawn, Billy!!!"
      "Get off my lawn, Anne-Marie!!!"
      "Get off my lawn, The Incredible Bulk!!!")
; Use `&` to allow dynamic number of arguments

(defn favorite-things
  [name & things]
  (str "Hi, " name ", here are my favorite things: "
       (clojure.string/join ", " things)))

(favorite-things "Doreen" "gum" "shoes" "kara-te")
; => "Hi, Doreen, here are my favorite things: gum, shoes, kara-te"
; Using mix of argument and rest parameters

(defn chooser
  [[first-choice second-choice & unimportant-choices]]
  (println (str "Your first choice is: " first-choice))
  (println (str "Your second choice is: " second-choice))
  (println (str "We're ignoring the rest of your choices. "
                "Here they are in case you need to cry over them: "
                (clojure.string/join ", " unimportant-choices))))

(chooser ["Marmalade", "Handsome Jack", "Pigpen", "Aquaman"])
; => Your first choice is: Marmalade
; => Your second choice is: Handsome Jack
; => We're ignoring the rest of your choices. Here they are in case \
;    you need to cry over them: Pigpen, Aquaman
```

###### Function Body
```clojure
(defn illustrative-function
  []
  (+ 1 304)
  30
  "joe")

(illustrative-function)
; => "joe"

(defn number-comment
  [x]
  (if (> x 6)
    "Oh my gosh! What a big number!"
    "That number's OK, I guess"))

(number-comment 5)
; => "That number's OK, I guess"

(number-comment 7)
; => "Oh my gosh! What a big number!"
```

###### Anonymous Functions

```clojure
(fn [param-list]
  function body)

(map (fn [name] (str "Hi, " name))
     ["Darth Vader" "Mr. Magoo"])
; => ("Hi, Darth Vader" "Hi, Mr. Magoo")

((fn [x] (* x 3)) 8)
; => 24

;; Function call
(* 8 3)

;; Anonymous function
#(* % 3)
```

###### Returning Functions

```clojure
(defn inc-maker
  "Create a custom incrementor"
  [inc-by]
  #(+ % inc-by))

(def inc3 (inc-maker 3))

(inc3 7)
; => 10
```

###### Putting it all together

```clojure
(def asym-hobbit-body-parts [{:name "head" :size 3}
                             {:name "left-eye" :size 1}
                             {:name "left-ear" :size 1}
                             {:name "mouth" :size 1}
                             {:name "nose" :size 1}
                             {:name "neck" :size 2}
                             {:name "left-shoulder" :size 3}
                             {:name "left-upper-arm" :size 3}
                             {:name "chest" :size 10}
                             {:name "back" :size 10}
                             {:name "left-forearm" :size 3}
                             {:name "abdomen" :size 6}
                             {:name "left-kidney" :size 1}
                             {:name "left-hand" :size 2}
                             {:name "left-knee" :size 2}
                             {:name "left-thigh" :size 4}
                             {:name "left-lower-leg" :size 3}
                             {:name "left-achilles" :size 1}
                             {:name "left-foot" :size 2}])

(defn matching-part
  [part]
  {:name (clojure.string/replace (:name part) #"^left-" "right-")
   :size (:size part)})

(def asym-hobbit-body-parts [{:name "head" :size 3}
                             {:name "left-eye" :size 1}
                             {:name "left-ear" :size 1}
                             {:name "mouth" :size 1}
                             {:name "nose" :size 1}
                             {:name "neck" :size 2}
                             {:name "left-shoulder" :size 3}
                             {:name "left-upper-arm" :size 3}
                             {:name "chest" :size 10}
                             {:name "back" :size 10}
                             {:name "left-forearm" :size 3}
                             {:name "abdomen" :size 6}
                             {:name "left-kidney" :size 1}
                             {:name "left-hand" :size 2}
                             {:name "left-knee" :size 2}
                             {:name "left-thigh" :size 4}
                             {:name "left-lower-leg" :size 3}
                             {:name "left-achilles" :size 1}
                             {:name "left-foot" :size 2}])


(defn matching-part
  [part]
  {:name (clojure.string/replace (:name part) #"^left-" "right-")
   :size (:size part)})

➊ (defn symmetrize-body-parts
  "Expects a seq of maps that have a :name and :size"
  [asym-body-parts]
➋   (loop [remaining-asym-parts asym-body-parts 
         final-body-parts []]
➌     (if (empty? remaining-asym-parts) 
      final-body-parts
➍       (let [[part & remaining] remaining-asym-parts] 
➎         (recur remaining 
               (into final-body-parts
                     (set [part (matching-part part)])))))))

;The symmetrize-body-parts function (starting at ➊) employs a general strategy that is common in functional programming. Given a sequence (in this case, a vector of body parts and their sizes), the function continuously splits the sequence into a head and a tail. Then it processes the head, adds it to some result, and uses recursion to continue the process with the tail.
;We begin looping over the body parts at ➋. The tail of the sequence will be bound to remaining-asym-parts. Initially, it’s bound to the full sequence passed to the function: asym-body-parts. We also create a result sequence, final-body-parts; its initial value is an empty vector.
;If remaining-asym-parts is empty at ➌, that means we’ve processed the entire sequence and can return the result, final-body-parts. Otherwise, at ➍ we split the list into a head, part, and tail, remaining.
;At ➎, we recur with remaining, a list that gets shorter by one element on each iteration of the loop, and the (into) expression, which builds our vector of symmetrized body parts.

(symmetrize-body-parts asym-hobbit-body-parts)
; => [{:name "head", :size 3}
;      {:name "left-eye", :size 1}
;      {:name "right-eye", :size 1}
;      {:name "left-ear", :size 1}
;      {:name "right-ear", :size 1}
;      {:name "mouth", :size 1}
;      {:name "nose", :size 1}
;      {:name "neck", :size 2}
;      {:name "left-shoulder", :size 3}
;      {:name "right-shoulder", :size 3}
;      {:name "left-upper-arm", :size 3}
;      {:name "right-upper-arm", :size 3}
;      {:name "chest", :size 10}
;      {:name "back", :size 10}
;      {:name "left-forearm", :size 3}
;      {:name "right-forearm", :size 3}
;      {:name "abdomen", :size 6}
;      {:name "left-kidney", :size 1}
;      {:name "right-kidney", :size 1}
;      {:name "left-hand", :size 2}
;      {:name "right-hand", :size 2}
;      {:name "left-knee", :size 2}
;      {:name "right-knee", :size 2}
;      {:name "left-thigh", :size 4}
;      {:name "right-thigh", :size 4}
;      {:name "left-lower-leg", :size 3}
;      {:name "right-lower-leg", :size 3}
;      {:name "left-achilles", :size 1}
;      {:name "right-achilles", :size 1}
;      {:name "left-foot", :size 2}
;      {:name "right-foot", :size 2}]
```

#### Loop

```clojure
(loop [iteration 0]
  (println (str "Iteration " iteration))
  (if (> iteration 3)
    (println "Goodbye!")
    (recur (inc iteration))))
; => Iteration 0
; => Iteration 1
; => Iteration 2
; => Iteration 3
; => Iteration 4
; => Goodbye!
```

#### Regular Expressions

```clojure
#"regular-expression"

(re-find #"^left-" "left-eye")
; => "left-"

(re-find #"^left-" "cleft-chin")
; => nil

(re-find #"^left-" "wongleblart")
; => nil

(defn matching-part
  [part]
  {:name (clojure.string/replace (:name part) #"^left-" "right-")
   :size (:size part)})
(matching-part {:name "left-eye" :size 1})
; => {:name "right-eye" :size 1}]

(matching-part {:name "head" :size 3})
; => {:name "head" :size 3}]
```

#### Reduce

```clojure
;; sum with reduce
(reduce + [1 2 3 4])
; => 10
```

#### Example

```clojure
(defn hit
  [asym-body-parts]
  (let [sym-parts (➊better-symmetrize-body-parts asym-body-parts)
        ➋body-part-size-sum (reduce + (map :size sym-parts))
        target (rand body-part-size-sum)]
    ➌(loop [[part & remaining] sym-parts
           accumulated-size (:size part)]
      (if (> accumulated-size target)
        part
        (recur remaining (+ accumulated-size (:size (first remaining))))))))
```
