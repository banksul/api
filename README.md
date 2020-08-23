
# API Pública Banksul
Documentação da API para usuários e parceiros do BankSul.

1. [Visão geral](#Visão-geral)
1. [Checkout](#checkout)
    1. [Checkout - Criar](#Criar)
    1. [Checkout - Atualizar](#checkout_update)
    1. [Checkout - Consultar](#checkout_search)
    1. [Checkout - Pesquisa Personalizada](#checkout_all)
1. [Dúvidas](#dúvidas)

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

  * `id` - codigo da fatura gerada  (Integer | required)
  * `title` - Descrição da fatura (string | required)
  * `value` - valor da fatura (decimal | required)
  * `email` - e-mail válido (string | optional) * caso seja informado será enviado o link de pagamento para o email.
  * `url_return` - url de retorno * Quando houver alguma interação neste fatura será disparado um get na url informada.

Exemplo em PHP:

```php

  $token = "";
  $data = json_encode(array(
        "id" => 81112,
        "email" => 'email@email.com',
        "title" => "Leadership: Practical Leadership Skills",
        "value" => 98.78,
        "url_return" => "http://www.seusite.com/retorno/pay/81112",
       ));

    $curl = curl_init();

    curl_setopt_array($curl, array(
      CURLOPT_URL => 'https://api.banksul.com/v2/checkout/create',
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
        'Authentication:  '.$token
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
    "id": "81112",
    "id_banksul": "OxhyaDbKuHVQ",
    "date": "2020-08-22 00:06:12",
    "date_payment": null,
    "status": "waiting",
    "title": "Leadership: Practical Leadership Skills",
    "value": "98.78",
    "url_return": "http://www.meusite.com/retorno/pay/81112",
    "checkout_banksul": "https://app.banksul.com/payment/7c6feb806a501f5dd7c3b3d5fc540cafff3cf9cf77cd5deb0ca66d51302990b1"
  }
}
```


### Checkout - Atualizar
Para realizar uma alteração na ordem gerada é necessário enviar o mesmo token e id enviado anteriormente. Para atualizar a ordem precisa está em aberto com status 'waiting'. Caso já o status seja 'paid' não será possível atualizar.

Endpoint: `/v2/checkout/update`

Query Parameters:

  * `id` - codigo da fatura gerada  (Integer | required)
  * `title` - Descrição da fatura (string | required)
  * `value` - valor da fatura (decimal | required)
  * `email` - e-mail válido (string | optional) * caso seja informado será enviado o link de pagamento para o email.
  * `url_return` - Url de retorno * Quando houver alguma interação neste fatura será disparado um get na url informada.

Exemplo em PHP:

```php


  $token = "";
  $data = json_encode(array(
        "id" => 81112,
        "email" => 'email2@email.com',
        "title" => "Leadership: Practical Leadership Skills",
        "value" => 100.78,
        "url_return" => "http://www.seusite.com/retorno/pay/81112",
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
        'Authentication:  '.$token
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
    "id": "81112",
    "id_banksul": "OxhyaDbKuHVQ",
    "date": "2020-08-22 00:06:12",
    "date_payment": null,
    "status": "waiting",
    "title": "Leadership: Practical Leadership Skills",
    "value": "98.78",
    "url_return": "http://www.meusite.com/retorno/pay/81112",
    "checkout_banksul": "https://app.banksul.com/payment/7c6feb806a501f5dd7c3b3d5fc540cafff3cf9cf77cd5deb0ca66d51302990b1"
  }
}
```



## Dúvidas

Mande um e-mail para suporte@banksul.com em caso de dúvidas ou sugestões de melhorias e integrações da API.
