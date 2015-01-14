# Цепочка вызовов

[importance 2]

Есть объект "лестница" ladder:

```js
var ladder = {
  step: 0,
  up: function() {  // вверх по лестнице
    this.step++;
  },
  down: function() {  // вниз по лестнице
    this.step--;
  },
  showStep: function() { // вывести текущую ступеньку
    alert(this.step);
  }
};
```

Сейчас, если нужно последовательно вызвать несколько методов объекта, это можно сделать так:

```js
ladder.up();
ladder.up();
ladder.down();
ladder.showStep(); // 1
```

Модифицируйте код методов объекта, чтобы вызовы можно было делать цепочкой, вот так:

```js
ladder.up().up().down().up().down().showStep();  // 1
```

Такой подход называется "чейнинг" (chaining) и используется, например, во фреймворке jQuery.
