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

| Método | Endpoint | [`errors`](#errors) | [`fields`](#fields) | [`metadata`](#metadata) | [`result`](#result) | [`results`](#results) | [`status`](#status) |
|:-:|:-|:-:|:-:|:-:|:-:|:-:|:-:|
| GET | [`/:model`](#get-model) | :heavy_check_mark:¹ | :heavy_check_mark: | :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| GET | `/:model/new` | :heavy_check_mark:² | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | :heavy_check_mark: |
| GET | `/:model/filters` | :x: | :heavy_check_mark: | :x: | :x: | :x: | :heavy_check_mark: |
| GET | `/:model/:id` | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :heavy_check_mark: |
| GET | `/:model/:id/edit` | :heavy_check_mark:² | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :heavy_check_mark: |
| POST | `/:model` | :heavy_check_mark: | :x: | :x: | :x: | :x: | :heavy_check_mark: |
| DELETE | `/:model/:id` | :x: | :x: | :x: | :x: | :x: | :heavy_check_mark: |
| PATCH | `/:model/:id` | :heavy_check_mark: | :x: | :x: | :x: | :x: | :heavy_check_mark: |
| PUT | `/:model/:id` | :heavy_check_mark: | :x: | :x: | :x: | :x: | :heavy_check_mark: |

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

## Respostas

### `errors`

Objeto que deverá respeitar a mesma estrutura de chaves do [`result`](#result) que, ao invés de trazer os valores atuais, deverá retornar uma ou mais mensagens de erro, sempre dentro de um _array_. 

``` json
{
  "errors": {
    "title": ["O título não pode ficar em branco."],
    "author": {
      "name": ["Informe o nome completo.", "O nome precisa possuir 2 ou mais caracteres."]
    }
  }
}
```

### `fields`

Cada item deste _array_ contém o detalhamento de um campo do formulário.

``` json
{
  "fields": [
    {
      "name": "title",
      "label": "Título",
      "hint": "Obrigatório",
      "default": "",
      "type": "text"
    }
  ]
}
```

As chaves existentes para cada campo são:

| Chave | Tipo | Campos | Obrigatório | Descrição |
|:-:|:-:|:-:|:-:|:-|
| `default` | Todos | Todos | Não | Indica o valor inicial do campo quando não há valor atual. |
| `entity` | `String` | `upload` | Sim | Define o caminho para o armazenamento dos arquivos. |
| `extensions` | `Array` | `upload` | Não | Lista das extensões de arquivo permitidas para upload. Exemplo: `['jpg', 'png']`. |
| `hint` | `String` | Todos | Não | Texto de ajuda para complementar o rótulo. |
| `label` | `String` | Todos | Não | Rótulo do campo. Quando não informado, assume o valor de `name`. |
| `mask` | `String` | `text` | Não | Define uma máscara para o campo, pode se utilizar uma pré-definida ou utilizar as regras para a criação de uma máscara personalizada. |
| `max` | `Number` | `decimal`, `number`, `percent` ou `money` | Não | Numeral máximo. |
| `max_length` | `Number` | `text` ou `textarea` | Não | Número máximo de caracteres. |
| `min` | `Number` | `decimal`, `number`, `percent` ou `money` | Não | Numeral mínimo. |
| `min_length` | `Number` | `text` ou `textarea` | Não | Número mínimo de caracteres. |
| `multiple` | `Boolean` | `select` ou `upload` | Não | Indica se serão aceitas múltiplas respostas ou múltiplos arquivos. |
| `name` |  `String` | Todos | Sim | Nome do campo em _snake case_. |
| `options` | `Array` | `checkbox`, `radio` ou `select` | Sim | Uma coleção de objetos no formato `{ label: 'Item', value: 'item' }`, onde cada um define um item para seleção. É possível ainda criar grupos de opções adicionando a chave `children: []` no item e as respectivas opções. |
| `places` | `Number` | `decimal`, `percent` ou `money` | Não | Número de casas decimais. O padrão é `2`. |
| `prefix` | `String` | `date`, `datetime`, `decimal`, `money`, `number`, `percent`, `select`, `text` ou `time`. | Não | Texto que aparece antes do campo. |
| `read_only` | `Boolean` | Todos | Não | Desabilita a edição no campo. |
| `required` | `Boolean` | Todos | Não | Indica a obrigatoriedade do campo. |
| `suffix` | `String` | `date`, `datetime`, `decimal`, `money`, `number`, `percent`, `select`, `text` ou `time`. | Não | Texto que aparece após o campo. |
| `type` | `String` | Todos | Não | Define o tipo de campo (um dos listados abaixo). Quando omitido o padrão é `text`. |

E os possíveis tipos são:

- `boolean`
- `checkbox`
- `color`
- `date`
- `datetime`
- `decimal`
- `editor`
- `money`
- `number`
- `percent`
- `radio`
- `select`
- `text`
- `textarea`
- `time`
- `upload`

### `metadata`

Dados extras ao `result`/`results`.

``` json
{
  "metadata": {
    "foo": "bar"
  }
}
```

### `result`

Objeto contendo os campos e seus respectivos valores atuais.

``` json
{
  "result": {
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
        "foo": false,
        "bar": true
      }
    ]
  }
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
