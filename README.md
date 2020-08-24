
# API Pública Banksul
Documentação da API para usuários e parceiros do BankSul.

1. [Visão geral](#visao-geral)
1. [Checkout](#checkout)
    1. [Checkout - Criar](#checkout---criar)
    1. [Checkout - Atualizar](#checkout---atualizar)
    1. [Checkout - Status](#checkout---status)
    1. [Checkout - Buscar](#checkout---buscar)
2. [User](#user)    
3. [Dúvidas](#dúvidas)

## Visão geral

Para utilização da API é necessário:

  1. Criar uma conta em https://app.banksul.com
  2. Validar sua conta enviando seus documentos.
  3. Criar um token com os dados do servidor de origem. Acesse o menu API -> Criar Token.


Todas as respostas da API são em realizadas em JSON e as requisições são feitas no endpoint:

<pre>https://api.banksul.com</pre>

Todas as chamadas para a API tem no retorno, o atributo success, data e message.
1. success - Quando bem sucedida você receberá TRUE, em caso de algum erro, você receberá como retorno FALSE;
2. data -  Você receberá um objeto com os dados solicitados.
3. message - Em caso de algum error, enviaremos a informação.

Conforme os exemplos abaixo:
<pre>
{
  "success": true,
  "data": {}
}
</pre>

<pre>
{
  "success": false,
  "message": "Mensagem de erro..."
}
</pre>


## Checkout

* Gera uma nova ordem de pagamento em sua conta, disponibilizando ao usuário um ambiente seguro com opção de pagamento em boleto bancário ou Bitcoin.
* Não é necessário o usuário possuir uma conta na Banksul para acessar a página de pagamento.
* O sistema dispara automaticamente um e-mail para o usuário com o link do pagamento.
* Caso ele tenha uma conta na Banksul, ele pode utilizar o código gerado e efetuar o pagamento diretamente com o saldo em sua conta.

### Checkout - Criar

Endpoint: `/v2/checkout/create`

Query Parameters:

  * `id` - codigo da fatura gerada  (integer | required)
  * `title` - Descrição da fatura (string | required)
  * `value` - valor da fatura (decimal | required)
  * `email` - e-mail válido (string | optional) * caso seja informado será enviado o link de pagamento para o email.
  * `url_return` - url de retorno * Quando houver alguma interação neste fatura será disparado um get na url informada.

Exemplo em PHP:

```php

$data = json_encode(array(
      "id" => 1001,
      "email" => 'email@email.com',
      "title" => "Leadership: Practical Leadership Skills",
      "value" => 99.78,
      "url_return" => "http://www.meusite.com/retorno/checkout/1001",
     ));

  $curl = curl_init();

  curl_setopt_array($curl, array(
    CURLOPT_URL => 'https://api.banksul.com/v2/checkout/update',
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_ENCODING => "",
    CURLOPT_MAXREDIRS => 10,
    CURLOPT_TIMEOUT => 0,
    CURLOPT_FOLLOWLOCATION => false,
    CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
    CURLOPT_CUSTOMREQUEST => "POST",
    CURLOPT_POSTFIELDS => $data,
    CURLOPT_HTTPHEADER => array(
      "Content-Type: application/json",
      'Content-Length: ' . strlen( $data ),
      'Authentication: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1aWQiOiIzZDkxNGY5MzQ4YzljYzBmZjhhNzk3MTY3MDBiOWZjZDRkMmYzZTcxMTYwODAwNGViOGYxMzhiY2JhN2YxNGQ5IiwiZW1haWwiOiJ3c2NvcGVsQGdtYWlsLmNvbSIsImlhdCI6MTU5Nzk2ODAwNH0.pCpOf-OKpw8NMXhFqvYWHiXCdgOUqhbuT1jSGbGDZpI'
    ),
  ));

  $response = curl_exec($curl);
  $err = curl_error($curl);

  curl_close($curl);

  if ($err) {
    echo "cURL Error #:" . $err;
  } else {
    echo $response;
  }

```

Resposta:

```json
{
  "success": true,
  "data": {
    "id": "1001",
    "id_banksul": "Ox99dae535",
    "date": "2020-08-23 19:17:18",
    "date_payment": null,
    "status": "waiting",
    "customer": {},
    "title": "Leadership: Practical Leadership Skills",
    "value": "99.78",
    "url_return": "http:\/\/www.meusite.com\/retorno\/checkout\/1001",
    "checkout_banksul": "https:\/\/app.banksul.com\/payment\/1c596b4fdcac980e9c5d2c3a79ee92ec"
  }
}
```

* `checkout_banksul` -  Redirecione seu cliente para esse link para concluir o pagamento.


### Checkout - Atualizar
Para realizar uma alteração na ordem gerada é necessário enviar o mesmo token e id enviado anteriormente. Para atualizar a ordem precisa está em aberto com status 'waiting'. Caso já o status seja 'paid' não será possível atualizar.

Endpoint: `/v2/checkout/update`

Query Parameters:

  * `id` - codigo da fatura gerada  (integer | required)
  * `title` - Descrição da fatura (string | required)
  * `value` - valor da fatura (decimal | required)
  * `email` - e-mail válido (string | optional) * caso seja informado será enviado o link de pagamento para o email.
  * `url_return` - Url de retorno * Quando houver alguma interação neste fatura será disparado um get na url informada.

Exemplo em PHP:

```php
$data = json_encode(array(
      "id" => 1001,
      "email" => 'email@email.com',
      "title" => "Leadership: Practical Leadership Skills - Premium",
      "value" => 999.78,
      "url_return" => "http://www.meusite.com/retorno/checkout/1001",
     ));

  $curl = curl_init();

  curl_setopt_array($curl, array(
    CURLOPT_URL => 'https://api.banksul.com/v2/checkout/update',
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_ENCODING => "",
    CURLOPT_MAXREDIRS => 10,
    CURLOPT_TIMEOUT => 0,
    CURLOPT_FOLLOWLOCATION => false,
    CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
    CURLOPT_CUSTOMREQUEST => "POST",
    CURLOPT_POSTFIELDS => $data,
    CURLOPT_HTTPHEADER => array(
      "Content-Type: application/json",
      'Content-Length: ' . strlen( $data ),
      'Authentication: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1aWQiOiIzZDkxNGY5MzQ4YzljYzBmZjhhNzk3MTY3MDBiOWZjZDRkMmYzZTcxMTYwODAwNGViOGYxMzhiY2JhN2YxNGQ5IiwiZW1haWwiOiJ3c2NvcGVsQGdtYWlsLmNvbSIsImlhdCI6MTU5Nzk2ODAwNH0.pCpOf-OKpw8NMXhFqvYWHiXCdgOUqhbuT1jSGbGDZpI'
    ),
  ));

  $response = curl_exec($curl);
  $err = curl_error($curl);

  curl_close($curl);

  if ($err) {
    echo "cURL Error #:" . $err;
  } else {
    echo $response;
  }

```

Resposta:

```json
{
  "success": true,
  "data": {
    "id": "1001",
    "id_banksul": "Ox99dae535",
    "date": "2020-08-23 19:17:18",
    "date_payment": null,
    "status": "waiting",
    "title": "Leadership: Practical Leadership Skills - Premium",
    "value": "999.78",
    "url_return": "http:\/\/www.meusite.com\/retorno\/checkout\/1001",
    "checkout_banksul": "https:\/\/app.banksul.com\/payment\/1c596b4fdcac980e9c5d2c3a79ee92ec"
  }
}
```


### Checkout - Status

Endpoint: `/v2/checkout/status/:id`

Query Parameters:

  * `id` - codigo da fatura gerada  (integer | required)

Exemplo em PHP:

```php

  $id = 1001;
  $curl = curl_init();

  curl_setopt_array($curl, array(
    CURLOPT_URL => 'https://api.banksul.com/v2/checkout/status/'.$id,
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_ENCODING => "",
    CURLOPT_MAXREDIRS => 10,
    CURLOPT_TIMEOUT => 0,
    CURLOPT_FOLLOWLOCATION => false,
    CURLOPT_HTTP_VERSION =>
    CURL_HTTP_VERSION_1_1,
    CURLOPT_CUSTOMREQUEST => "GET",
    CURLOPT_HTTPHEADER => array(
      "Content-Type: application/json",
      'Authentication: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1aWQiOiIzZDkxNGY5MzQ4YzljYzBmZjhhNzk3MTY3MDBiOWZjZDRkMmYzZTcxMTYwODAwNGViOGYxMzhiY2JhN2YxNGQ5IiwiZW1haWwiOiJ3c2NvcGVsQGdtYWlsLmNvbSIsImlhdCI6MTU5Nzk2ODAwNH0.pCpOf-OKpw8NMXhFqvYWHiXCdgOUqhbuT1jSGbGDZpI'
    ),
  ));

  $response = curl_exec($curl);
  $err = curl_error($curl);

  curl_close($curl);

  if ($err) {
    echo "cURL Error #:" . $err;
  } else {
    echo $response;
  }

```

Resposta:

```json
{
  "success": true,
  "data": {
    "id": "1001",
    "id_banksul": "Ox99dae535",
    "title": "Leadership: Practical Leadership Skills - Premium",
    "value": "999.78",
    "customer": {
      "name": "Bruno Camargos Lipaus",
      "user": "brunolipaus",
      "email": "email@outlook.com"
    },
    "date": "2020-08-23 19:17:18",
    "date_payment": null,
    "status": "waiting",
    "url_return": "http:\/\/www.meusite.com\/retorno\/checkout\/1001"
  }
}
```
** customer - name, user, email. Caso tenha acontecido alguma interação no checkout, você terá os dados do cliente. Não significa que o cliente tenha efetuado o pagamento. Confira o status da transação.
** status - paid (pago) | canceled (cancelado) | waiting (aguardando pagamento ou interação do cliente.)


### Checkout - Buscar

Endpoint: `/v2/checkout/search`

Query Parameters:

* `date_min` - Data inicial (date)
* `date_max` - Data Final   (date)
* `date_payment_min` - Data de pagamento inicial (date)
* `date_payment_max` - Data de pagamento Final   (date)
* `limit` - Quantidade de registros retornados (integer | required)
* `order_by` - ASC ou DESC (string | required)

Exemplo em PHP:

```php

$data = json_encode(array(
      'date_min' => '2020-01-01',
      'date_max' => '2020-12-31',
      'date_payment_min' => '',
      'date_payment_max' => '',
      'limit' => 100,
      'order_by' => 'asc', //1 - crescente 2- decrescente
     ));

     $curl = curl_init();

     curl_setopt_array($curl, array(
       CURLOPT_URL => 'https://api.banksul.com/v2/checkout/search',
       CURLOPT_RETURNTRANSFER => true,
       CURLOPT_ENCODING => "",
       CURLOPT_MAXREDIRS => 10,
       CURLOPT_TIMEOUT => 0,
       CURLOPT_FOLLOWLOCATION => false,
       CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
       CURLOPT_CUSTOMREQUEST => "POST",
       CURLOPT_POSTFIELDS => $data,
       CURLOPT_HTTPHEADER => array(
         "Content-Type: application/json",
         'Content-Length: ' . strlen( $data ),
         'Authentication: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1aWQiOiIzZDkxNGY5MzQ4YzljYzBmZjhhNzk3MTY3MDBiOWZjZDRkMmYzZTcxMTYwODAwNGViOGYxMzhiY2JhN2YxNGQ5IiwiZW1haWwiOiJ3c2NvcGVsQGdtYWlsLmNvbSIsImlhdCI6MTU5Nzk2ODAwNH0.pCpOf-OKpw8NMXhFqvYWHiXCdgOUqhbuT1jSGbGDZpI'
       ),
     ));

     $response = curl_exec($curl);
     $err = curl_error($curl);

     curl_close($curl);

     if ($err) {
       echo "cURL Error #:" . $err;
     } else {
       echo $response;
     }

```

Resposta:

```json
{
  "success": true,
  "data": [
    {
      "id": "1904",
      "id_banksul": "Ox07be7096",
      "title": "Leadership: Practical Leadership Skills",
      "value": "298.78",
      "customer": [],
      "date": "2020-08-22 22:43:08",
      "date_payment": null,
      "status": "waiting",
      "url_return": "http:\/\/www.meusite.com\/retorno\/pay\/1903"
    },
    {
      "id": "1903",
      "id_banksul": "Ox70b94000",
      "title": "Leadership: Practical Leadership Skills",
      "value": "198.78",
      "customer": [],
      "date": "2020-08-22 19:38:42",
      "date_payment": null,
      "status": "waiting",
      "url_return": "http:\/\/www.meusite.com\/retorno\/pay\/1903"
    }
  ]
}
```
** customer - name, user, email. Caso tenha acontecido alguma interação no checkout, você terá os dados do cliente. Não significa que o cliente tenha efetuado o pagamento. Confira o status da transação.
** status - paid (pago) | canceled (cancelado) | waiting (aguardando pagamento ou interação do cliente.)
** date_payment - trata-se de todas as faturas pagas.




## User

Endpoint: `/v2/user/search/:user`

Query Parameters:

  * `user` - nome do usuário no banksul.  (string | required)

Exemplo em PHP:

```php

$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'https://api.banksul.com/v2/user/search/brunox',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => "",
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => false,
  CURLOPT_HTTP_VERSION =>
  CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => "GET",
  CURLOPT_HTTPHEADER => array(
    "Content-Type: application/json",
    'Authentication: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1aWQiOiIzZDkxNGY5MzQ4YzljYzBmZjhhNzk3MTY3MDBiOWZjZDRkMmYzZTcxMTYwODAwNGViOGYxMzhiY2JhN2YxNGQ5IiwiZW1haWwiOiJ3c2NvcGVsQGdtYWlsLmNvbSIsImlhdCI6MTU5Nzk2ODAwNH0.pCpOf-OKpw8NMXhFqvYWHiXCdgOUqhbuT1jSGbGDZpI'
  ),
));

$response = curl_exec($curl);
$err = curl_error($curl);

curl_close($curl);

if ($err) {
  echo "cURL Error #:" . $err;
} else {
  echo $response;
}

```

Resposta:

```json
{
  "success": true,
  "data": {
    "name": "Bruno Souza Oliveira",
    "user": "brunox",
    "email": "bruno@bruno.com"
  }
}
```

* Documentação e endereço somente é disponibilizado na API checkout após o pagamento pelo cliente.


## Dúvidas

Mande um e-mail para suporte@banksul.com em caso de dúvidas ou sugestões de melhorias e integrações da API.
