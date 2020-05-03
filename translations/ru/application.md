---
description: Инстанс приложения, обычно, обозначает Fiber приложение.
---

# 🚀 Приложение

## New

Этот метод создает новое **приложение** названного экземпляра.  
Вы можете передать необязательные [параметры](application.md#settings) при создании новой копии

{% code title="Signature" %}
```go
fiber.New(settings ...Settings) *App
```
{% endcode %}

{% code title="Example" %}
```go
package main

import "github.com/gofiber/fiber"

func main() {
    app := fiber.New()

    // ...

    app.Listen(3000)
}
```
{% endcode %}

## Settings

Вы можете передать настройки приложения при вызове метода `New`.

{% code title="Example" %}
```go
func main() {
    // Pass Settings creating a new instance
    app := fiber.New(&fiber.Settings{
        Prefork:       true,
        CaseSensitive: true,
        StrictRouting: true,
        ServerHeader:  "Fiber",
    })

    // ...

    app.Listen(3000)
}
```
{% endcode %}

Или измените настройки после инициализации `приложения`.

{% code title="Example" %}
```go
func main() {
    app := fiber.New()

    // Or change Settings after creating an instance
    app.Settings.Prefork = true
    app.Settings.CaseSensitive = true
    app.Settings.StrictRouting = true
    app.Settings.ServerHeader = "Fiber"

    // ...

    app.Listen(3000)
}
```
{% endcode %}

**Настройки** **полей**

| Свойство                  | Тип                                                  | Описание                                                                                                                                                                                                                                                                      | По умолчанию      |
|:------------------------- |:---------------------------------------------------- |:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |:----------------- |
| Prefork                   | `bool`                                               | Позволяет использовать опцию сокета[`SO_REUSEPORT`](https://lwn.net/Articles/542629/). Это приведет к появлению нескольких Go процессов прослушивания на одном порту. узнайте больше о [сокетах в шардинге](https://www.nginx.com/blog/socket-sharding-nginx-release-1-9-1/). | `false`           |
| ServerHeader              | `string`                                             | Включает HTTP-заголовок `Server` с заданным значением.                                                                                                                                                                                                                        | `""`              |
| StrictRouting             | `bool`                                               | Если этот параметр включен, маршрутизатор обрабатывает `/foo` и `/foo/`, как разные маршруты. В противном случае, маршрутизатор обрабатывает `/foo` и `/foo/`, как одинаковые маршруты.                                                                                       | `false`           |
| CaseSensitive             | `bool`                                               | Если включено, `/Foo` и `/foo` являются разными маршрутами. Когда отключено, `/Foo` и `/foo` обрабатываются одинаково.                                                                                                                                                        | `false`           |
| Immutable                 | `bool`                                               | Когда включено, все значения, возвращаемые методами контекста, не изменяются. По умолчанию они действительны до тех пор, пока вы не вернетесь из обработчика, см. проблему [\#185](https://github.com/gofiber/fiber/issues/185).                                            | `false`           |
| BodyLimit                 | `int`                                                | Устанавливает максимально допустимый размер для тела запроса. Если размер превышает сконфигурированный предел, то он отправляет `413 - Request Entity Too Large` в качестве ответа.                                                                                           | `4 * 1024 * 1024` |
| Concurrency               | `int`                                                | Максимальное количество одновременных подключений.                                                                                                                                                                                                                            | `256 * 1024`      |
| DisableKeepalive          | `bool`                                               | Отключение keep-alive соединений, приведет к закрытию входящих соединений после отправки первого ответа клиенту                                                                                                                                                               | `false`           |
| DisableDefaultDate        | `bool`                                               | Если установлено значение true, то заголовок даты по умолчанию будет исключён из ответа.                                                                                                                                                                                      | `false`           |
| DisableDefaultContentType | `bool`                                               | Если установлено значение true, то заголовок Content-Type по умолчанию будет исключён из ответа.                                                                                                                                                                              | `false`           |
| DisableStartupMessage     | `bool`                                               | When set to true, it will not print out the fiber ASCII and "listening" on message                                                                                                                                                                                            | `false`           |
| ETag                      | `bool`                                               | Enable or disable ETag header generation, since both weak and strong etags are generated using the same hashing method \(CRC-32\). Weak ETags are the default when enabled.                                                                                                 | `false`           |
| TemplateEngine            | `func(raw string, bind interface{}) (string, error)` | You can specify a custom template function to render different template languages. See our [**Template Middleware**](middleware.md#template) _\*\*_for presets.                                                                                                         | `nil`             |
| TemplateFolder            | `string`                                             | A directory for the application's views. If a directory is set, this will be the prefix for all template paths. `c.Render("home", data) -> ./views/home.pug`                                                                                                               | `""`              |
| TemplateExtension         | `string`                                             | If you preset the template file extension, you do not need to provide the full filename in the Render function: `c.Render("home", data) -> home.pug`                                                                                                                       | `"html"`          |
| ReadTimeout               | `time.Duration`                                      | The amount of time allowed to read the full request including body. Время ожидания по умолчанию не ограничено.                                                                                                                                                                | `nil`             |
| WriteTimeout              | `time.Duration`                                      | The maximum duration before timing out writes of the response. Время ожидания по умолчанию не ограничено.                                                                                                                                                                     | `nil`             |
| IdleTimeout               | `time.Duration`                                      | The maximum amount of time to wait for the next request when keep-alive is enabled. If IdleTimeout is zero, the value of ReadTimeout is used.                                                                                                                                 | `nil`             |

## Static

Используйте метод **Static** для обработки статических файлов, таких как **изображения**, **CSS** и **JavaScript**.

{% hint style="info" %}
По умолчанию, **Static** будет обрабатывать`index.html` файлы в ответ на запрос в каталоге.
{% endhint %}

{% code title="Signature" %}
```go
app.Static(prefix, root string, config ...Static) // => with prefix
```
{% endcode %}

Используйте следующий код для отображения файлов в каталоге `./public`

{% code title="Example" %}
```go
app.Static("/", "./public")

// => http://localhost:3000/hello.html
// => http://localhost:3000/js/jquery.js
// => http://localhost:3000/css/style.css
```
{% endcode %}

Чтобы обслуживать файлы из нескольких каталогов, вы можете использовать метод **Static** несколько раз.

{% code title="Example" %}
```go
// Serve files from "./public" directory:
app.Static("/", "./public")

// Serve files from "./files" directory:
app.Static("/", "./files")
```
{% endcode %}

{% hint style="info" %}
Используйте кэш реверсивных прокси, типа [**NGINX**](https://www.nginx.com/resources/wiki/start/topics/examples/reverseproxycachingexample/), для улучшения производительности статических ассетов.
{% endhint %}

Вы можете использовать любой виртуальный префикс пути \(_где путь фактически не существует в файловой системе_\) для файлов, которые обслуживаются методом **Static**, указав путь префикса для статического каталога, как показано ниже:

{% code title="Example" %}
```go
app.Static("/static", "./public")

// => http://localhost:3000/static/hello.html
// => http://localhost:3000/static/js/jquery.js
// => http://localhost:3000/static/css/style.css
```
{% endcode %}

Если вы хотите иметь немного больше контроля над настройками для обработки статических файлов. Вы можете использовать структуру `fiber.Static` для включения конкретных настроек.

{% code title="fiber.Static{}" %}
```go
// Static represents settings for serving static files
type Static struct {
    // Transparently compresses responses if set to true
    // This works differently than the github.com/gofiber/compression middleware
    // The server tries minimizing CPU usage by caching compressed files.
    // It adds ".fiber.gz" suffix to the original file name.
    // Optional. Default value false
    Compress bool
    // Enables byte range requests if set to true.
    // Optional. Default value false
    ByteRange bool
    // Enable directory browsing.
    // Optional. Значение по умолчанию: false.
    Browse bool
    // Index file for serving a directory.
    // Optional. Значение по умолчанию "index.html".
    Index string
}
```
{% endcode %}

{% code title="Example" %}
```go
app.Static("/", "./public", fiber.Static{
  Compress:   true,
  ByteRange:  true,
  Browse:     true,
  Index:      "john.html"
})
```
{% endcode %}

## HTTP Methods

Маршрутизирует HTTP-запрос, где **METHOD** является [HTTP методом](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) запроса.

{% code title="Signatures" %}
```go
// HTTP methods support :param, :optional? and *wildcards
// You are required to pass a path to each method
app.All(path string, handlers ...func(*Ctx)) *Fiber
app.Get
app.Put
app.Post
app.Head
app.Patch
app.Trace
app.Delete
app.Connect
app.Options

// Use() will only match the beggining of each path
// i.e. "/john" will match "/john/doe", "/johnnnn"
// Use() does not support :param & :optional? in path
app.Use(handlers ...func(*Ctx))
app.Use(prefix string, handlers ...func(*Ctx)) *Fiber
```
{% endcode %}

{% code title="Example" %}
```go
app.Use("/api", func(c *fiber.Ctx) {
  c.Set("X-Custom-Header", random.String(32))
  c.Next()
})
app.Get("/api/list", func(c *fiber.Ctx) {
  c.Send("I'm a GET request!")
})
app.Post("/api/register", func(c *fiber.Ctx) {
  c.Send("I'm a POST request!")
})
```
{% endcode %}

## Group

You can group routes by creating a `*Group` struct.

**Signature**

```go
app.Group(prefix string, handlers ...func(*Ctx)) *Group
```

**Example**

```go
func main() {
  app := fiber.New()

  api := app.Group("/api", cors())  // /api

  v1 := api.Group("/v1", mysql())   // /api/v1
  v1.Get("/list", handler)          // /api/v1/list
  v1.Get("/user", handler)          // /api/v1/user

  v2 := api.Group("/v2", mongodb()) // /api/v2
  v2.Get("/list", handler)          // /api/v2/list
  v2.Get("/user", handler)          // /api/v2/user

  app.Listen(3000)
}
```

## Listen

Привязки и прослушивание соединений по указанному адресу. Это может быть `int` для порта или `string` для адреса.

{% code title="Signature" %}
```go
app.Listen(address interface{}, tls ...*tls.Config) error
```
{% endcode %}

{% code title="Examples" %}
```go
app.Listen(8080)
app.Listen("8080")
app.Listen(":8080")
app.Listen("127.0.0.1:8080")
```
{% endcode %}

Чтобы включить **TLS/HTTPS**, вы можете добавить [**TLS конфиг**](https://golang.org/pkg/crypto/tls/#Config).

{% code title="Example" %}
```go
cer, err := tls.LoadX509KeyPair("server.crt", "server.key")
if err != nil {
    log.Fatal(err)
}
config := &tls.Config{Certificates: []tls.Certificate{cer}}

app.Listen(443, config)
```
{% endcode %}

## Serve

Вы можете передать свои [`net.Listener`](https://golang.org/pkg/net/#Listener) с помощью метода `Serve`.

{% code title="Signature" %}
```go
app.Serve(ln net.Listener, tls ...*tls.Config) error
```
{% endcode %}

{% hint style="warning" %}
**Serve** не поддерживает функцию [**Prefork**](application.md#settings).
{% endhint %}

{% code title="Example" %}
```go
if ln, err = net.Listen("tcp4", ":8080"); err != nil {
    log.Fatal(err)
}

app.Serve(ln)
```
{% endcode %}

## Test

Тестирование вашего приложения осуществляется методом **Test**. Используйте этот метод для создания `_test.go` файлов или когда вам нужно отладить вашу логику маршрутизации. По умолчанию тайм-аут `200 мс`. Если вы хотите полностью отключить тайм-аут, то передайте `-1` в качестве второго аргумента.

{% code title="Signature" %}
```go
app.Test(req *http.Request, msTimeout ...int) (*http.Response, error)
```
{% endcode %}

{% code title="Example" %}
```go
// Create route with GET method for test:
app.Get("/", func(c *Ctx) {
  fmt.Println(c.BaseURL())              // => http://google.com
  fmt.Println(c.Get("X-Custom-Header")) // => hi

  c.Send("hello, World!")
})

// http.Request
req, _ := http.NewRequest("GET", "http://google.com", nil)
req.Header.Set("X-Custom-Header", "hi")

// http.Response
resp, _ := app.Test(req)

// Do something with results:
if resp.StatusCode == 200 {
  body, _ := ioutil.ReadAll(resp.Body)
  fmt.Println(string(body)) // => Hello, World!
}
```
{% endcode %}
