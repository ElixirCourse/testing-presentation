---

#### Тестване в Elixir

---

### Защо трябва да пишем тестове?

---

* Защото ръчното тестване е бавно
* За да хващаме регресии
* За да тестваме какво става при грешки
* За да можем по-лесно да рефакторираме
* За да имаме по-добър дизайн на кода си
* За да документираме поведението на кода си

---

### Защо хората не пишат тестове

---

* Когато бързаме тестовете ни се струват като бреме
* Ако в проекта няма тестове е по-трудно да се започне да се пишат
* Не сме сигурно какво точно да тестваме
* Усещаме, че тестовете ни пречат когато променяме код

---

### Какви видове тестове има?

За да адресираме горните проблеми е добре да видим какви тестове можем да пишем

---

* Unit тестове
* Функционални тестове
* Интеграционни тестове
* Performance тестове

---

### Unit тестове

* Много изолирани
* Обхваща малка част от кода - модул или компонент
* Ако промяна в модул A води до счупени тестове на модул B, то най-вероятно нямаме правилната изолация
* Много лесно се тестват edge cases и error handling

---

### Функционални тестове

* Термина е наложен от Ruby on Rails света
* Тестват на по-високо ниво - например контролер на web приложение
* По-бавни са от unit тестовете, но обикновенно не прекалено много
* Тестването на edge cases става по-трудно

---

### Интеграционни тестове

* Тестват функционалност обхващата по-обширен стек
* Например web приложение със JS frontend и Elixir backend
* Могат да бъдат много бавни
* Много трудно е да се тестват edge cases
* Обикновено покриват основни фийчъри и happy paths
* Могат да бъдат нестабилни заради големината на стека

---

### Performance тестове

* Много различни от по-горните примери
* Много рядко биват автоматизирани и пускани на всеки къмит
* Обикновено се правят преди големи release-и
* Най-добре е ако можем да правим performance tests на unit ниво
* Обикновено setup-а на тези тестове е сложно и обхваща много компоненти

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
  - doctest/1
  - Mock-ове

---

### ExUnit

  - стартираме `ExUnit.start()`
  - use-ва се като use-нем `use ExUnit.Case`
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
  # За Борко:
  # https://github.com/elixir-lang/elixir/issues/3580
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

```elixir
mock(HTTPClient, :get, to_return: %{..., "temperature" => 20, ...})
```

---

##### Не толкова бързо!

---

##### Решението.

---

```elixir
defmodule MyApp.WeatherReporter do
  @weather_client = Application.get_env(:my_app, :weather_client)

  def is_it_cold(city) do
    {:ok, t} = @weather_client.get_temperature(city)
    report(t)
  end

  ...
end
```

---

##### Остава да нагласим клиента за времето.

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

  # Всичките ни тестове ще питат Емо Чолаков за времето
  @moduletag :weather_client 

  # Долу в тестовете вече питаме Емо Чолаков
end
```

---

##### Използвахме модулен атрибут, за да можем да отделим този тест ето така:

---

- Казваме на теста да не се пуска с останалите:

```elixir
# test/test_helper.exs
ExUnit.configure exclude: [:weather_client]
ExUnit.start()
```

---

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
