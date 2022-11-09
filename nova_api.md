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

### [<span style="color: #1966a8">GET</span>] Get Accesses: `/accesses/count`

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

## Observações:

