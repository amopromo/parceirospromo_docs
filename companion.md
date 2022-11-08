# Companion

## Resumo:
O Companion é um microsserviço para registro de acessos em um determinado cliente.

Iremos disponibilizar um script do nosso serviço para que o registro de acessos seja contabilizado no parceirospromo. Junto com o script, também será disponibilizado a hash da plataforma, para que os acessos sejam registrados para aquela plataforma em específico.

## Servidores:
O serviço possui dois servidores de forma a tornar possível a integração de testagem do registro de acessos. Os servidor são:

- **Homologação:** https://cmp-dev.pcr.rec.br/
- **Produção:** https://cmp.pcr.rec.br/

## Instalação e execução:
A instalação do Companion se dá pela inserção do script disponibilizado com a hash única (uuid) para o e-commerce a ser integrado.

O script segue a seguinte estrutura:

```javascript
(function(c,o,m,r,a,d,e){
r=o.createElement('script');r.async=1;r.src='<SERVIDOR>'+c.location.search;
r.id="cmp";a=o.getElementsByTagName('script')[0];a.parentNode.insertBefore(r,a);
d=o.createElement('input');d.type='hidden';d.id='eh';d.value=m;
e=o.getElementsByTagName('body')[0];e.appendChild(d);
})(window,document,'<HASH DO E-COMMERCE>');
```

Um exemplo de script pronto para ser usado em produção:

```javascript
(function(c,o,m,r,a,d,e){
r=o.createElement('script');r.async=1;r.src='https://cmp.pcr.rec.br/cmp'+c.location.search;  // Servidor de produção
r.id="cmp";a=o.getElementsByTagName('script')[0];a.parentNode.insertBefore(r,a);
d=o.createElement('input');d.type='hidden';d.id='eh';d.value=m;
e=o.getElementsByTagName('body')[0];e.appendChild(d);
})(window,document,'0000000a-111b-222c-333d-44444444444e');  // Hash de e-commerce no formato adequado
```

Para o devido funcionamento do registro de acessos, o script acima deve ser inserido na página de entrada do e-commerce parceiro, na maioria dos casos em uma landing page. Com o script nesta página, o registro de acessos já estará implementado e os dados já estarão sendo salvos em nossa base.

## Observações:

### 1 - Formato da URL:
O formato da URL para que o Companion registre os acesos é o seguinte:

[https:/ecommerce.com/?utm_medium=utm_medium&pcrid=código-numérico-do-nosso-afiliado&utm_source=utm_source&pcrtt=múltiplas-tags,separadas-por-vírgula](https:/ecommerce.com/?utm_medium=utm_medium&pcrid=código-numérico-do-nosso-afiliado&utm_source=utm_source&pcrtt=múltiplas-tags,separadas-por-vírgula)

Exemplo:
[https:/parceirospromo.com.br/?utm_medium=afiliado&pcrid=2554&utm_source=site-blog&pcrtt=tag-teste,outra-tag](https:/parceirospromo.com.br/?utm_medium=afiliado&pcrid=2554&utm_source=site-blog&pcrtt=tag-teste,outra-tag)

### 2 - Cookies:
O Companion NÃO gera qualquer tipo de cookie durante seu funcionamento.
