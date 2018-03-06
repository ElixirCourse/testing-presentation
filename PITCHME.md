---

#### TDD is dead.

---?image=assets/troll.jpg&size=auto 90%

---

### Тестване в Elixir

---

### Но първо - малко въпроси!

---

### Какво правихме миналия път?

---

### Какво правихме по-миналия път?

---

```elixir
[[{x, _} | a] | b] = [[{9, 10} , 2, 3], 0]
x  # ?
a  # ?
b  # ?
```

---

```elixir
[[{x, _} | a] | b] = [[{9, 10} , 2, 3], 0]
x  # 9
a  # [2, 3]
b  # [0]
```

---

### Какво е map reduce?

---

Cons a (Cons b (Cons ... (Cons z Nil)...))

---

### Защо небете е синьо?

---?image=assets/troll.jpg&size=auto 90%

---

### Защо тестваме?

---

### Защото трябва.

---

### Това е всичко.

---

#### Може да ходим да пием еликсири.

---?image=assets/beers.jpg&size=auto 90%

---?image=assets/troll.jpg&size=auto 90%

---

#### По-лесно да пишем код.

---

#### Пази ни от регресии.

---

#### Документация.

---

#### Да откриваме по-хубав интерфейс на кода ни.

---

#### [Four-phase testing.](http://xunitpatterns.com/Four%20Phase%20Test.html)

---

  - Setup
  - Exercise
  - Verify
  - Teardown

---

### А сега в Elixir код

---
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

---

### Тестване в Elixir

  - Вградена библиотека ExUnit
  - Примерите ни в документациятата може да се използва за тестове
  - Fixture-и
  - Mock-ове

---

### ExUnit

  - стартираме `ExUnit.start()`
  - използва се като напишем `use ExUnit.Case`
  - магическите думи `async: true`
  - използваме макрото `test`


---

#### Пример:

```elixir
# Файл: assertion_test.exs

# 1) Стартирам ExUnit
ExUnit.start()
```

---

#### Пример:

```elixir
# Файл: assertion_test.exs

# 2) Създаваме нов модул и use-ваме ExUnit.
defmodule AssertionTest do
  # 3) Опцията "async: true" ще стартира всички тестови
  #    случаи(модули) конкуретно. Индивидуалните тестове
  #    в един модул ще останат последователно.
  # [За Борко]
  # (https://github.com/elixir-lang/elixir/issues/3580)
  use ExUnit.Case, async: true

  # ...
end
```

---

#### Пример:

```elixir
# Файл: assertion_test.exs

defmodule AssertionTest do
  # ...

  # 4) Използваме макроса "test" вместо "def" за четимост.
  test "the truth" do
    assert true
  end
end
```

---

### assert

---

### assert е макро, за да ни позволява такива неща:

---

```elixir
assert value
assert value == 4
assert value <= 5
assert value in [1, 2, 3, 4]
```

---

#### Има и други

  - assert_receive
  - assert_in_delta
  - assert_raise
  - assert_received
  - refute

---

#### [Ще си ги видите](https://hexdocs.pm/ex_unit/ExUnit.Assertions.html#content)

---

### `doctest/1`

---

#### Документация като тестове, what the f***?

---

#### Най-често за да даваме примери на колегите, които ще ни ползват кода.

---

##### Включително и ние след седмица.

---

##### Оставаме сигурни, че документацията ни важи за последната версия на кода.

---

### Syntax

---

- всеки тест почва на нов ред
- започва с `iex>`
- поддържа многоредови примери с `...>`

---

```elixir
iex> 1+1
2
```

---

##### или

---

```elixir
iex> Enum.map [1, 2, 3], fn(x) ->
...>   x * 2
...> end
[2, 4, 6]
```

---

### Mock-ване в Elixir

---

#### Не mock-ваме в Elixir

---?image=assets/troll.jpg&size=auto 90%

---

#### Използваме mock-ове :)

---

##### Нека дам пример

---

##### Проблемът.

---

```elixir
defmodule MyApp.WeatherReporter do
  def is_it_cold(city) do
    {:ok, t} = MyApp.WeatherClient.get_temperature(city)
    report(t)
  end

  defp report(n) when is_integer(n) and n < 10,
                 do: "Don't go outside"
  defp report(n) when is_integer(n),
                 do: "Go outside, if that's your thing"
end
```

---

##### Какво бихме направили, за да го изтестваме това?

---

`mock(HTTPClient, :get, to_return: %{..., "temperature" => "20", ...})`

---

##### Не толкова бързо!

---

##### Решението.

---

```elixir
defmodule MyApp.WeatherReporter do
  @weather_client = Application.get_env(:my_app, :weather_client)

  def is_it_cold(city) do
    {:ok, temperature} = @weather_client.get_temperature(city)
    report(temperature)
  end

  defp report(n) when is_integer(n) and n < 10, do: "Don't go outside"
  defp report(n) when is_integer(n), do: "Go outside, if that's your thing"
  defp report(_), do: "Try again maybe?"
end
```

---

##### Остава да нагласим weather клиента.

---

```elixir
# In config/dev.exs
config :my_app, :weather_client, MyApp.Weather.Sandbox

# In config/test.exs
config :my_app, :weather_client, MyApp.WeatherMock

# In config/prod.exs
config :my_app, :weather_client, MyApp.WeatherClient
```

---

#### Вече сме готови

---

#### Какво правим, ако все пак искаме да тестваме клиента?

---

```elixir
defmodule MyApp.WeatherClient.Test do
  use ExUnit.Case, async: true

  # All tests will ping the twitter API
  @moduletag :weather_client

  # Write your tests here
end
```

---

##### Използвахме модулен атрибут, за да можем да отделим този тест ето така:

---

- Казваме теста да не се пуска с останалите:

```elixir
# test/test_helper.exs
ExUnit.configure exclude: [:weather_client]
ExUnit.start()
```

- Ако искаме все пак да ги пуснем(CI)

```elixir
mix test --include twitter_api
```

---

#### Малко библиотеки да разгледате за вкъщи

---

#### [ExUnit](https://hexdocs.pm/ex_unit/ExUnit.html) разбира се

---

#### [Mockery](https://hexdocs.pm/mockery/readme.html) - спестява бойлерплейта на горния пример с мокове

---
#### [Mox](https://hexdocs.pm/mox/Mox.html) - същото с повече експлицитност

---

#### [ExMachina](https://hexdocs.pm/ex_machina/ExMachina.html) - test data

---

#### [use Plug.Test](https://hexdocs.pm/plug/Plug.Test.html) - за боравене с Plug

---

#### [Oще статии по проблема с Mock-ването](http://blog.plataformatec.com.br/2015/10/mocks-and-explicit-contracts/)

---?image=assets/beers.jpg&size=auto 90%
