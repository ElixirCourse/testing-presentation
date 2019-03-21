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
* Не сме сигурни какво точно да тестваме
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

### Как да започнем да пишем тестове?

---

#### Ако започваме нов проект

* Опитайте да не тествате ръчно
* Ако правите ръчно тестване, опитайте се да напишете тест с него
* Ще видите, че много бързо ефективноста ви започва да се вдига
* Ще видите, че изведнъж откривате регресиите много бързо

---?image=assets/red_green_refactor.jpg

---

### Когато кода нарастне

* Вече няма да ви е страх да правите промени
* Започнете да добавяте integration tests за основните неща
* Опитвайте се да пишете тестовете преди имплементацията
* Така ще мислите за интерфейса, а не за имплементацията

---

#### Ако имаме съществуващ проект

---

#### В началото ще е трудно

* Започнете с писане на тестове при оправяне на бъгове
* Ако пишете нова функционалност, напишете интеграционен тест
* Когато променяте модули, започнете да пишете unit тестове за промените
* Най-вероятно ще имате проблеми с изолацията и мокването
* Набележете си места, където се налага по-голям рефактор и разчитайте на integration тестове до тогава

---

#### След като наберете малко скорост

* Говорете с мениджъра си защо тестовете са важни
* Обяснете, че те ще подобрят качеството и скороста на разработка
* Настоявайте за формално заделено време от спринта за technical debt
* Бъдете практични и се концентрирайте върху най-чупливите места
* Обикновено това са местата където кода се променя най-много

---

### Генерални съвети

* Тествайте интерфейса, не имплементацията
  * Не пишете тестове на private функции
  * Не моквайте private функции
* Не допускайте flaky тестове
* Никога не диплойвайте ако тестовете не минават
* Тестовете трябва да се пускат с една команда
* Избягвайте глобален стейт в тестовете си

---

### CI/CD is the king

* Тестовете трябва да се пускат за всички branch-ове
* Трябва да се вижда лесно дали тестовете минават за един PR
* Ако тестовете в master не минават не се диплойва
* Диплоймънта трябва да е с натискане на един бутон
* Използвайте docker за да унифицирате local и CI средите

---

### Project setup

* Дръжте setup-а на проекта под контрол
* Ако някой иска да пусне проекта, това трябва да става с няколко лесни стъпки
* Поддържайте консистентна политика за мокване
  * Съгласете се как е добре да се моква
  * Съгласете се как ще правите dependency injection
* Следете code coverage-а, но не се прехласвайте по него

---

### Как да пишем тестове

---

#### [Four-phase testing.](http://xunitpatterns.com/Four%20Phase%20Test.html)

---

* Setup
* Exercise
* Verify
* Teardown

---

### А сега в Elixir код

---
```elixir
defmodule ServerTest do
  use ExUnit.Case, async: true

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

### Setup

* setup vs setup_all
* Можем да подаваме контекст

---

```elixir
defmodule ContextDemoTest do
  def setup(context) do
    {:ok, [:client, "My custom client"]}
  end

  @tag user_id, 1
  test "the client is setup properly", %{client: client, user_id: user_id} do
    assert client == "My custom client"
    assert user_id == 1
  end
end
```

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

### `doctest/2`

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
defmodule DoctestDemo do
  @doc """
  Square a collection of numbers.

  ## Examples

    iex> DoctestDemo.square([1, 2, 3])
    [1, 4, 9]

  """
  def square(list) do
    Enum.map(list, fn x -> x * x end)
  end
end
```

---

### Тестване на процеси

```elixir
defmodule ProcessTest do
  use ExUnit.Case, async: true

  def ping_server(receiver) do
    receive do
      msg ->
        send(receiver, msg)
        ping_server(receiver)
    end
  end

  setup do
    pid = spawn(__MODULE__, :ping_server, [self()])

    {:ok, server: pid}
  end

  test "receiving messages", %{server: server} do
    send(server, :hello)

    assert_receive :hello
  end
end```

---

### Mock-ване в Elixir

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

##### Ако искаме все пак да ги пуснем(CI)

```elixir
mix test --include weather_client
```

---

#### Малко библиотеки да разгледате за вкъщи

---

#### [ExUnit](https://hexdocs.pm/ex_unit/ExUnit.html) разбира се

---

#### [Mockery](https://hexdocs.pm/mockery/readme.html) - спестява бойлерплейта на горния пример с мокове
#### [Mox](https://hexdocs.pm/mox/Mox.html) - друг подход за concurrent mocks

---

#### [ExMachina](https://hexdocs.pm/ex_machina/ExMachina.html) - test data

---

#### [use Plug.Test](https://hexdocs.pm/plug/Plug.Test.html) - за боравене с Plug

---

#### [Oще статии по проблема с Mock-ването](http://blog.plataformatec.com.br/2015/10/mocks-and-explicit-contracts/)

---?image=assets/beers.jpg&size=auto 90%
