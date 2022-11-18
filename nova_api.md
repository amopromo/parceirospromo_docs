# Nova API do ParceirosPromo

## Resumo:
A nova API do ParceirosPromo é um serviço que possibilita a consulta de acessos na plataforma, além de consulta, inserção e atualização de pedidos.

O objetivo da utilização da API é que o fornecedor tenha controle sobre os dados fundamentais da integração com a nossa plataforma, de forma que possa estruturar e organizar toda esta informação da forma como for mais produtiva para seu negócio.

Todas as rotas e suas respectivas ações estão disponíveis para todos os fornecedores, portanto cada fornecedor pode utilizar todas as funcionalidades da API, ou apenas aquelas que acrescentam mais ao seu negócio.

Para ter acesso à API o fornecedor necessita apenas utilizar sua chave única na requisição, conforme será descrito detalhadamente abaixo, para ter acesso aos dados correspondentes à sua plataforma de forma integral. Com poderá consultar seus dados de acesso e venda.

## Servidores:
O serviço possui dois servidores de forma a tornar possível a integração de testagem de suas funcionalidades. Os servidores são:

- **Homologação:** https://hom.parceirospromo.com.br/api
- **Produção:** https://www.parceirospromo.com.br/api

## Headers e Autenticação:
A autenticação deve ser feita com Basic Auth usando a chave única disponibilizada para o fornecedor codificada em base64.

Os headers da requisição devem ser enviados no seguinte formato:

```json
{
    "Authorization": "Basic <chave única>",
    "Content-Type": "application/json"
}
```

Um exemplo de headers considerando que a chave única de fornecedor seja `parceirospromo`:

```json
{
    "Authorization": "Basic cGFyY2Vpcm9zcHJvbW8=",
    "Content-Type": "application/json"
}
```

Com os headers envidados neste formato, as requisições serão autenticadas no serviço.

## Paginação:

Devido a possibilidade de haver uma grande quantidade de dados nas rotas de listagem de acessos e vendas, foi necessário implementar uma paginação para que os fornecedores tivessem seus dados exibidos de forma mais organizada.

A estrutura da paginação é a seguinte:

```json
{
    "current_page": 50,
    "total_pages": 100,
    "has_previous": true,
    "has_next": true,
    "total_items": 1500,
    "data": [...]  // 15 registros
}
```

Nesta estrutura as chaves representam o seguinte:

- **current_page:** Página atual da paginação;
- **total_pages:** Quantidade total de páginas da busca atual;
- **has_previous:** Se a página atual possui página anterior. Retorna verdadeiro para todas as páginas, exceto a primeira;
- **has_next:** Se a página atual possui página posterior. Retorna verdadeira para todas as páginas, exceto a última;
- **data:** Onde os registros são disponibilizados. A quantidade padrão é de 15 registros por página;

Para exibir uma nova página basta passar o parâmetro `page` com o valor definido para o número da página requerida. Valores inválidos como números negativos ou números maiores do que a quantidade de páginas da pesquisa retornarão os resultados da primeira página (página 1).

## Rotas:
A iniciar pelas rotas de acesso, abaixo será descrito como as rotas estão estruturadas.

### [<span style="color: #1966a8">GET</span>] Get Accesses: `/accesses`

Rota responsável por exibir os registros de acessos de um fornecedor na plataforma. São parâmetros obrigatórios `begin` e `end` que representam respectivamente, a data de início e a data de fim da busca.

#### Exemplo de requisição:

A URL da requisição deve seguir o seguinte formato:

https://www.parceirospromo.com.br/api/accesses?begin=data_inicial&end=data_final&page=página

Um exemplo de requisição para o servidor de produção:

https://www.parceirospromo.com.br/api/accesses?begin=2022-10-01&end=2022-10-04&page=5

A requisição acima retornaria os registros de acessos criados entre o dia 01/10/2022 às 00:00 e 04/10/2022 às 23:59.

#### Dados retornados pela API:

A resposta retorna algumas informações sobre o ambiente que o usuário realizou o acesso, mas de informações no contexto do ParceirosPromo as informações de nossa plataforma são:

- **id (int):** ID do acesso;
- **uuid (string):** UUID gerado no registro do acesso;
- **pcrid (int):** ID do afiliado que gerou o acesso;
- **ecommerceHash (string):** ID do fornecedor em nossa plataforma;
- **tags ([]string):** Tags inseridas pelo afiliado durante a divulgação do link para monitoramento da conversão;
- **created_at (string):** Data de criação do acesso.

#### Retorno da API:

Um exemplo dos retornos da API para esta consulta:

```json
{
    "current_page": 1,
    "total_pages": 10,
    "has_previous": false,
    "has_next": true,
    "total_items": 150,
    "data": [
        {
            "id": 987654321,
            "uuid": "0000000a-111b-222c-333d-44444444444e",
            "pcrid": 2554,
            "ecommerceHash": "id_do_fornecedor",
            "utmSource": "utm_source",
            "utmMedium": "utm_medium",
            "tags": [
                "tag-teste",
                "outra-tag"
            ],
            "created_at": "2022-10-04T23:59:44.465152Z",
            "href": "https://www.parceirospromo.com.br/?utm_medium=utm_medium&pcrid=2554&utm_source=utm_source&pcrtt=tag-teste,outra-tag",
            "referrer": "",
            "cookie": "Informações de Cookie",
            "userAgent": "Mozilla/5.0 (Linux; Android 11; SAMSUNG SM-A207M) AppleWebKit/537.36 (KHTML, like Gecko) SamsungBrowser/18.0 Chrome/99.0.4844.88 Mobile Safari/537.36",
            "query": "?utm_medium=utm_medium&pcrid=2554&utm_source=utm_source&pcrtt=tag-teste,outra-tag",
            "device": "Samsung SM-A207M",
            "os": "Android 11",
            "browser": "Samsung Internet 18.0",
            "screen": {
                "availHeight": 892,
                "availWidth": 412,
                "height": 892,
                "width": 412,
                "colorDepth": 24,
                "pixelDepth": 24
            },
            "navigator": {
                "hardwareConcurrency": 8,
                "language": "pt-BR",
                "languages": [
                    "pt-BR",
                    "pt",
                    "en-US",
                    "en"
                ]
            },
            "geolocation": {
                "city": "São Paulo",
                "state": "SP",
                "country": "Brasil"
            }
        },
        ...
    ]
}
```

### [<span style="color: #1966a8">GET</span>] Get Accesses Count: `/accesses/count`

Rota responsável por exibir a quantidade total de acessos de um fornecedor na plataforma. São parâmetros obrigatórios `begin` e `end` que representam respectivamente, a data de início e a data de fim da busca.

#### Exemplo de requisição:

A URL da requisição deve seguir o seguinte formato:

https://www.parceirospromo.com.br/api/accesses/count?begin=data_inicial&end=data_final

Um exemplo de requisição para o servidor de produção:

https://www.parceirospromo.com.br/api/accesses/count?begin=2022-10-01&end=2022-10-04

A requisição acima retornaria a quantidade de acessos criados entre o dia 01/10/2022 às 00:00 e 04/10/2022 às 23:59.

#### Dados retornados pela API:

- **accesses_count (int):** Quantidade total de acessos naquele período.

#### Retorno da API:

```json
{
	"accesses_count": 150
}
```

### [<span style="color: #1966a8">GET</span>] Get Purchases: `/purchases`

Rota responsável por exibir os registros de vendas de um fornecedor na plataforma. São parâmetros obrigatórios `begin` e `end` que representam respectivamente, a data de início e a data de fim da busca.

#### Exemplo de requisição:

A URL da requisição deve seguir o seguinte formato:

https://www.parceirospromo.com.br/api/purchases?begin=data_inicial&end=data_final&status=codigo_do_status&page=página

Um exemplo de requisição para o servidor de produção:

https://www.parceirospromo.com.br/api/purchases?begin=2022-10-01&end=2022-10-04&status=1&page=5

A requisição acima retornaria os registros de vendas criadas entre o dia 01/10/2022 às 00:00 e 04/10/2022 às 23:59 com status aprovado (1).

#### Dados retornados pela API:

- **id (string):** ID da venda;
- **pcrid (int):** Id do afiliado;
- **status (int):** Status do pedido representado por:
  - 0 - Pendente;
  - 1 - Aprovado;
  - 2 - Reprovado.
- **purchase_date (string):** Data da realização da venda no sistema do fornecedor;
- **price (float):** Valor total do produto;
- **payment_commission_date (string):** Data de referência para o pagamento de comissão. Tem como finalidade definir que a comissão só deve ser paga após a data do check-in, por exemplo, para evitar que um afiliado receba comissão de produtos que virão a ser cancelados;
- **commission_base (float):** Valor base para que a comissão seja calculada. Este valor é específico para cada fornecedor. Alguns possuem o commission_base de 100%, ou seja, o mesmo valor do price, enquanto outros possuem bases de comissões menores como 90% ou 80% do valor total;
- **commission_value (float):** Valor da comissão gerada;
- **contact_name (string):** Nome do cliente;
- **tags ([]string):** Tags inseridas pelo afiliado durante a divulgação do link para monitoramento da conversão;
- **extra (dict):** Campo livre para que o fornecedor possa colocar dados essenciais para seu negócio que não são abrangidos pelos campos anteriores. Ex.: nome do produto, moeda de câmbio, valor do câmbio, etc.;
- **created_at (string):** Data de criação do registro no ParceirosPromo.

#### Retorno da API:

Um exemplo dos retornos da API para esta consulta:

```json
{
    "current_page": 5,
    "total_pages": 10,
    "has_previous": true,
    "has_next": true,
    "total_items": 150,
    "data": [
        {
            "id": "987654",
            "pcrid": 2554,
            "status": 1,
            "purchase_date": "2022-10-31T00:00:00Z",
            "price": 100.00,
            "payment_commission_date": "2022-12-01T00:00:00Z",
            "commission_base": 90.00,  // A base de comissão é 90% do valor total para este exemplo
            "commission_value": 9.00,  // Exemplo de comissão de 10%
            "contact_name": "João Alves",
            "tags": [
                "tag-teste",
                "outra-tag"
            ],
            "extra": {
                "check_in": "2022-11-29",
                "check_out": "2022-11-30",
                "currency": "USD",
                "exchange_rate": 5.00,
                "exchange_value": 20.00,
                "hotel": "Hotel Hello World"
            },
            "created_at": "2022-10-31T00:01:25.437499-03:00"
        },
        ...
    ]
}
```

### [<span style="color: #1966a8">GET</span>] Get Purchases Count: `/purchases/count`

Rota responsável por exibir a quantidade total de vendas de um fornecedor na plataforma. São parâmetros obrigatórios `begin` e `end` que representam respectivamente, a data de início e a data de fim da busca.

#### Exemplo de requisição:

A URL da requisição deve seguir o seguinte formato:

https://www.parceirospromo.com.br/api/purchases/count?begin=data_inicial&end=data_final&status=codigo_do_status

Um exemplo de requisição para o servidor de produção:

https://www.parceirospromo.com.br/api/purchases/count?begin=2022-10-01&end=2022-10-04&status=0

A requisição acima retornaria a quantidade de vendas criadas entre o dia 01/10/2022 às 00:00 e 04/10/2022 às 23:59 com status pendente (0).

#### Dados retornados pela API:

- **purchases_count (int):** Quantidade total de vendas naquele período.

#### Retorno da API:

```json
{
    "purchases_count": 150
}
```

### [<span style="color: #1db847">POST</span>] Insert Purchases: `/purchases/`

Rota responsável por receber novos pedidos do fornecedor. Todos os pedidos registrados no fornecedor que possua um "pcrid" (ID de afiliado do ParceirosPromo) devem ser enviados por esta rota para que a comissão do afiliado possa ser paga e ele continue divulgando a marca do fornecedor.

#### Parâmetros que devem ser enviados através a API:

- **id (string):** ID da venda;
- **pcrid (int):** Id do afiliado;
- **purchase_date (string):** Data da realização da venda no sistema do fornecedor;
- **status (int):** Status do pedido representado por:
  - 0 - Pendente;
  - 1 - Aprovado;
  - 2 - Reprovado.
- **price (float):** Valor total do produto;
- **payment_commission_date (string):** Data de referência para o pagamento de comissão. Tem como finalidade definir que a comissão só deve ser paga após a data do check-in, por exemplo, para evitar que um afiliado receba comissão de produtos que virão a ser cancelados;
- **commission_base (float):** Valor base para que a comissão seja calculada. Este valor é específico para cada fornecedor. Alguns possuem o commission_base de 100%, ou seja, o mesmo valor do price, enquanto outros possuem bases de comissões menores como 90% ou 80% do valor total;
- **contact_name (string):** Nome do cliente;
- **tags ([]string):** Tags inseridas pelo afiliado durante a divulgação do link para monitoramento da conversão;
- **extra (dict):** Campo livre (pode ser enviado vazio) para que o fornecedor possa colocar dados essenciais para seu negócio que não são abrangidos pelos campos anteriores. Ex.: nome do produto, moeda de câmbio, valor do câmbio, etc.;

#### Exemplo de requisição:

A requisição do método POST deve ser feita para https://www.parceirospromo.com.br/api/purchases/ no formato JSON.

Ex.:

```json
[{
    "id": "ABC123",
    "pcrid": 2554,
    "purchase_date": "2022-11-09",
    "status": 1,
    "price": 100.00,
    "payment_commission_date": "2022-12-01",
    "commission_base": 100.00,  // A base de comissão é 100% do valor total para este exemplo
    "contact_name": "Desenvolvimento AmoPromo",
    "tags": ["tag-teste", "outra-tag"],
    "extra": {
        "check_in": "2022-11-29",
        "check_out": "2022-11-30",
        "currency": "USD",
        "exchange_rate": 5.00,
        "exchange_value": 20.00,
        "hotel": "Hotel Hello World",
        "product": "Quarto duplo, tipo A"
    }
}]
```

Múltiplos pedidos podem ser incluídos em uma única requisição.

#### Retorno da API:

Em caso de sucesso (HTTP status 200):

```json
{
    "msg": "Registro(s) adicionado(s) com sucesso"
}
```

Para caso houver algum erro (HTTP status 400 ou 500):

```json
{
    "err": "<Mensagem de erro informando o que ocorreu>"
}
```

### [<span style="color:#d97c11 ">PUT</span>] Update Purchases: `/purchases/`

Rota responsável pela atualizar o status de pedidos. É a rota que deve ser usada para caso algum pedido enviado para a plataforma ParceirosPromo tenha sido aprovado ou cancelado. Um caso de uso muito comum é o envio de pedidos realizados com boleto bancário como método de pagamento serem enviados com o status pendente (0) para nossa plataforma e ter seus status atualizados para aprovado (1) ou reprovado (2) conforme o pagamento ou a expiração.

#### Parâmetros que devem ser enviados através da API:

- **id (string):** ID da venda;
- **status (int):** Status do pedido representado por:
  - 0 - Pendente;
  - 1 - Aprovado;
  - 2 - Reprovado.

#### Exemplo de requisição:

A requisição do método PUT deve ser feita para https://www.parceirospromo.com.br/api/purchases/ no formato JSON.

Ex.:

```json
[
    {
        "id": "ABC123",
        "status": 1  // Altera o status do pedido "ABC123" para aprovado
    },
    {
        "id": "9D8E8F7",
        "status": 2  // Altera o status do pedido "9D8EF7" para cancelado
    }
]
```

Múltiplos pedidos podem ser atualizados em uma única requisição.

#### Retorno da API:

Em caso de sucesso (HTTP status 200)

```json
{
    "msg": "Registro(s) atualizado(s) com sucesso"
}
```

Para caso houver algum erro (HTTP status 400 ou 500):

```json
{
    "err": "<Mensagem de erro informando o que ocorreu>"
}
```
