---
hidden: true
---

# Subgraph Query Examples



## Protocol Data



## Collection Tokens (Memecoins)

### All Meme Coins

<mark style="color:green;">`POST`</mark>&#x20;

\<Description of the endpoint>

**Headers**

| Name          | Value              |
| ------------- | ------------------ |
| Content-Type  | `application/json` |
| Authorization | `Bearer <token>`   |

**Body**

| Name   | Type   | Description      |
| ------ | ------ | ---------------- |
| `name` | string | Name of the user |
| `age`  | number | Age of the user  |

**Response**

{% tabs %}
{% tab title="200" %}
```json
{
  "id": 1,
  "name": "John",
  "age": 30
}
```
{% endtab %}

{% tab title="400" %}
```json
{
  "error": "Invalid request"
}
```
{% endtab %}
{% endtabs %}



## Users

{% tabs %}
{% tab title="cURL" %}
```javascript
const message = "hello world";
console.log(message);
```
{% endtab %}

{% tab title="Javascript" %}
```javascript
message = "hello world"
print(message)
```
{% endtab %}
{% endtabs %}

