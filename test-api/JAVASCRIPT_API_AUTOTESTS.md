# Автотесты API на JavaScript

## Что потребуется

- Node.js 18 или новее: в нём уже есть `fetch` и встроенный тестовый раннер.
- URL тестового окружения. Не используйте production для тестов, которые изменяют данные.
- Токен с минимально необходимыми правами, если API требует авторизацию.

Не храните URL, токены и пароли в тестах. Передавайте их через переменные окружения:

```powershell
$env:API_BASE_URL = "https://api.test.example.com"
$env:API_TOKEN = "test-token"
```

## Структура теста

Создайте файл, например `test-api\users.test.js`. Один тест должен проверять один сценарий: успешный ответ, ошибку валидации, отсутствие авторизации или неизвестный ресурс.

```js
import assert from "node:assert/strict";
import test from "node:test";

const baseUrl = process.env.API_BASE_URL;

if (!baseUrl) {
  throw new Error("Set the API_BASE_URL environment variable before running tests.");
}

async function request(path, options = {}) {
  const headers = {
    Accept: "application/json",
    ...options.headers,
  };

  if (process.env.API_TOKEN) {
    headers.Authorization = `Bearer ${process.env.API_TOKEN}`;
  }

  return fetch(new URL(path, baseUrl), {
    ...options,
    headers,
  });
}

test("GET /users/1 returns the requested user", async () => {
  const response = await request("/users/1");

  assert.equal(response.status, 200);
  assert.match(response.headers.get("content-type") ?? "", /application\/json/);

  const user = await response.json();
  assert.equal(user.id, 1);
  assert.equal(typeof user.name, "string");
});

test("POST /users rejects a request without a required name", async () => {
  const response = await request("/users", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({}),
  });

  assert.equal(response.status, 400);

  const body = await response.json();
  assert.ok(body.error);
});
```

Запустите тесты из корня репозитория:

```powershell
node --test test-api\users.test.js
```

## Что проверять

- Код ответа: `200`, `201`, `400`, `401`, `403`, `404` и другие, предусмотренные контрактом API.
- Нужные поля и типы в теле ответа, а не весь ответ целиком.
- Заголовки, например `content-type`.
- Ошибки для пустых, некорректных и граничных значений.
- Авторизацию: доступ без токена, с недостаточными правами и с разрешёнными правами.
- Побочные эффекты: созданные или изменённые данные.

## Изоляция данных

Создавайте уникальные тестовые данные, например добавляя случайный суффикс к email или имени. Если тест создал сущность, удаляйте её в конце теста. Тесты не должны зависеть от порядка запуска или данных, созданных другим тестом.

Для операций удаления и изменения используйте отдельное тестовое окружение и учётную запись с ограниченными правами.
