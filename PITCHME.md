#HSLIDE

#### TDD is dead.

#HSLIDE
![Image-Relative](assets/troll.png)

#HSLIDE

### Тестване в Elixir

#HSLIDE

### Но първо - малко въпроси!

#HSLIDE

![Image-Relative](assets/troll.png)

#HSLIDE

### Какво правихме миналия път?

#HSLIDE

### Какво правихме по-миналия път?

#HSLIDE

```elixir
[[{x, _} | a] | b] = [[{9, 10} , 2, 3], 0]
x  # ?
a  # ?
b  # ?
```

#HSLIDE

```elixir
[[{x, _} | a] | b] = [[{9, 10} , 2, 3], 0]
x  # 9
a  # [2, 3]
b  # [0]
```

#HSLIDE

### Какво е map reduce?

#HSLIDE

### Cons a (Cons b (Cons ... (Cons z Nil)...))

#HSLIDE

### Защо небете е синьо?

#HSLIDE
![Image-Relative](assets/troll.png)

#HSLIDE

### Защо тестваме?

#HSLIDE

### Защото трябва.

#HSLIDE

### Това са ми аргументите.

#HSLIDE
![Image-Relative](assets/troll.png)

#HSLIDE

#### По-лесно да пишем код.

#HSLIDE

#### Пази ни от регресии.

#HSLIDE

#### Документация.

#HSLIDE

#### Да откриваме по-хубав интерфейс на кода ни.

#HSLIDE

#### [Four-phase testing.](http://xunitpatterns.com/Four%20Phase%20Test.html)

#HSLIDE

  - Setup
  - Exercise
  - Verify
  - Teardown

#HSLIDE

### А сега в Elixir код

#HSLIDE
```elixir
defmodule ServerTest do
  use ExUnit.Case

  setup do
    start_server! # Setup
    on_exit(fn -> stop_server! end) # Teardown
  end

  test "that it returns 200" do
    result = get(route) # Exercise

    assert result == something # Verify
  end
end
```

#HSLIDE

### Тестване в Elixir

  - Вградена библиотека ExUnit
  - Примерите ни в документациятата може да се използва за тестове
  - Fixture-и
  - Mock-ове

#HSLIDE

### ExUnit

  - стартираме `ExUnit.start()`
  - използва се като напишем `use ExUnit.Case`
  - магическите думи `async: true`
  - използваме макрото `test`


#HSLIDE

#### Пример:

```elixir
# Файл: assertion_test.exs

# 1) Стартирам ExUnit
ExUnit.start()

# 2) Създаваме нов модул и use-ваме ExUnit.
defmodule AssertionTest do
  # 3) Опцията "async: true" ще стартира всички тестови случаи(модули) конкуретно.
  # Индивидуалните тестове в един модул ще останат последователно.
  # [За Борко](https://github.com/elixir-lang/elixir/issues/3580)
  use ExUnit.Case, async: true

  # 4) Използваме макроса "test" вместо "def" за четимост.
  test "the truth" do
    assert true
  end
end
```

#HSLIDE

### assert

#HSLIDE

### assert е макро, за да ни позволява такива неща:

#HSLIDE

```elixir
assert value
assert value == 4
assert value <= 5
assert value in [1, 2, 3, 4]
```

#HSLIDE

#### Има и други

  - assert_receive
  - assert_in_delta
  - assert_raise
  - assert_received
  - refute

#HSLIDE

#### [Ще си ги видите](https://hexdocs.pm/ex_unit/ExUnit.Assertions.html#content)

#HSLIDE

### `doctest/1`

#HSLIDE

#### Документация като тестове, what the f***?

#HSLIDE

#### Най-често за да даваме примери на колегите, които ще ни ползват кода.

#HSLIDE

##### Включително и ние след седмица.

#HSLIDE

##### Оставаме сигурни, че документацията ни важи за последната версия на кода.

#HSLIDE

### Syntax

#HSLIDE

- всеки тест почва на нов ред
- започва с `iex>`
- поддържа многоредови примери с `...>`

#HSLIDE

```elixir
iex> 1+1
2
```

#HSLIDE

##### или

#HSLIDE

```elixir
iex> Enum.map [1, 2, 3], fn(x) ->
...>   x * 2
...> end
[2, 4, 6]
```

#HSLIDE

### Mock-ване в Elixir

#HSLIDE

#### Не mock-ваме в Elixir

#HSLIDE
![Image-Relative](assets/troll.png)

#HSLIDE

## Използваме mock-ове :)

TODO: Finish mock example
TODO: Maybe property testing
