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

Rota responsável por...

## Observações:

