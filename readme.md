# API

Documentação do modelo oficial de API implementada pelos sistemas criados pela inovação Bild & Vitta.

## Premissas

1. O formato padrão para a troca de dados é o JSON.
2. A extensão do _endpoint_ é opcional para o formato padrão (`.json`) e, quando disponível, pode-se informar a extensão do padrão alternativo desejado, como `.xml` ou `.yml`.
3. Os _endpoints_ devem funcionar com ou sem a barra no final do endereço.
4. As chaves de envio e de retorno devem estar no padrão _snake case_.
5. Os endereços seguem o padrão REST.

## Endpoints

A tabela abaixo relaciona os _endpoints_ com as possibilidades de resposta. Sempre que um endereço possuir parâmetros de consulta, este estará documentado posteriormente.

| Método | Endpoint | [`errors`](#errors) | [`fields`](#fields) | [`result`](#result) | [`results`](#results) | [`status`](#status) |
|:-:|:-|:-:|:-:|:-:|:-:|:-:|
| GET | [`/:model`](#get-model) | :heavy_check_mark:¹ | :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| GET | `/:model/new` | :heavy_check_mark:² | :heavy_check_mark: | :x: | :x: | :heavy_check_mark: |
| GET | `/:model/filters` | :x: | :heavy_check_mark: | :x: | :x: | :heavy_check_mark: |
| GET | `/:model/:id` | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :heavy_check_mark: |
| GET | `/:model/:id/edit` | :heavy_check_mark:² | :heavy_check_mark: | :heavy_check_mark: | :x: | :heavy_check_mark: |
| POST | `/:model` | :heavy_check_mark: | :x: | :x: | :x: | :heavy_check_mark: |
| DELETE | `/:model/:id` | :x: | :x: | :x: | :x: | :heavy_check_mark: |
| PATCH | `/:model/:id` | :heavy_check_mark: | :x: | :x: | :x: | :heavy_check_mark: |
| PUT | `/:model/:id` | :heavy_check_mark: | :x: | :x: | :x: | :heavy_check_mark: |

1. Nestes casos, os erros corresponderão aos campos de filtro.
2. Ocasionalmente pode ser necessário que um formulário inicie com erros preestabelecidos.



### GET `/:model`

A requisição pode conter uma porção de parâmetros para paginar, filtrar ou ordenar os resultados.

| Chave | Tipo | Obrigatório | Descrição |
|:-:|:-:|:-:|:-|
| `limit` | `Number` | Não | Quantidade de itens que serão retornados. |
| `offset` | `Number` | Não | Número do _index_ do primeiro item que será retornado, utilizado para paginação. |
| `ordering` | `String` | Não | Nome dos campos que deverão ser ordenados. Devem ser informados na ordem de importância (do maior para o menor) separados por vírgula. Ao final de cada campo deve ser adicionado `_asc` para ascendente ou `_desc` para descendente. |
| `search` | `String` | Não | Buscar um resultado genérico em um ou mais campos. |
| `[filter]` | Qualquer | Não | Qualquer campo que puder ser filtrado. Caso o ambiente permita, pode-se incrementar o filtro com instruções, como `_gt` (_greater than_) para "maior que" ou `_eq` (_equal_) para "igual à". |

**Exemplo:**

```
/:model?limit=12&offset=24&ordering=name_asc,title_desc&search=Test&status_eq=true
```

Já a resposta poderá conter [`status`](#status), [`fields`](#fields), [`results`](#results) e [`errors`](#errors) na seguinte ordem:

``` json
{
  "status": {},
  "fields": [],
  "results": {},
  "errors": {}
}
```

## Respostas

### `errors`

Objeto que deverá respeitar a mesma estrutura de chaves do [`result`](#result) que, ao invés de trazer os valores atuais, deverá retornar uma ou mais mensagens de erro (para mais de uma mensagem, a chave deverá estar relacionada à um _array_ de  `String`). 

### `fields`

.

### `result`

Objeto contendo os campos e seus respectivos valores atuais.

``` json
{
  "id": "a1b2c3d4e5",
  "title": "Lorem ipsum dolor sit amet, consectetur adipiscing elit.",
  "author": {
    "name": "John Appleseed"
  },
  "fields": [
    {
      "foo": true,
      "bar": false
    },
    {
      "foo": true,
      "bar": false
    }
  ]
}
```

### `results`

Um _array_ contendo uma coleção de objetos semelhantes ao [`result`](#result).

### `status`

Um objeto contendo os detalhes da requisição.

| Chave | Tipo | Obrigatório | Descrição |
|:-:|:-:|:-:|:-|
| `code` | `Number` | Sim | Código do _status_ HTTP da requisição, como `200` ou `404`. |
| `text` | `String` | Não | Mensagem específica contendo detalhes da requisição, geralmente utilizada quando há erros de servidor ou mensagens de sucesso. |

Por exemplo, ao solicitar a remoção de um item:

``` json
{
  "status": {
    "code": 200,
    "text": "O item foi deletado com sucesso!"
  }
}
```
