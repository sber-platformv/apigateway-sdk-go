# API Gateway SDK для подписи запросов на Golang

Для вызова API опубликованных c режимом аутентификации APP, запрос должен быть подписан при помощи APP_KEY и SECRET_KEY.
Для подписи запросов используются API Gateay SDK на одном из поддерживаемых языков программирования.

## Пререквизиты

* Golang 1.17 и выше.

## Подключение SDK к проекту

1. Импортируйте SDK в проект.
    ```golang
        import (
        ...
        "github.com/sber-platformv/apigateway-sdk-go/core"
        ...
    	)
    ```

## Пример использования SDK

В примере используются следующие зависимости. Добавьте их в проект, если они отсутствуют:

```golang
    import (
        "github.com/sber-platformv/apigateway-sdk-go/core"
        "bytes"
        "fmt"
        "io/ioutil"
        "net/http"
    )
```

1. Создайте новый signer и передайте в него `APP_KEY` и `SECRET_KEY`.
    ```golang
    s := core.Signer{
      Key:    "key",
      Secret: "secret",
    }
    ```

2. Создайте запрос, в котором укажите метод, URI, заголовки и тело запроса.
    ```golang
    r, _ := http.NewRequest("POST", "https://my-domain.example.com/v1/test",
            ioutil.NopCloser(bytes.NewBuffer([]byte("foo=bar"))))
    r.Header.Add("content-type", "application/json; charset=utf-8")
    r.Header.Add("x-stage", "RELEASE")
    ```
  
3. Вызовите функцию для подписывания запроса и подготовьте клиента для совершения вызова. Функция автоматически добавит заголовки `X-Sdk-Date` и `Authorization`.
    ```golang
    s.Sign(r)
    fmt.Println(r.Header)
    client := http.DefaultClient
    ```

4. Обратитесь к API и просмотрите ответ.
    ```golang
    resp, err := client.Do(r)
    if err != nil {
      fmt.Println(err)
    }

    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
      fmt.Println(err)
    }

    fmt.Println(string(body))
    ```

