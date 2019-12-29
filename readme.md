# API

Documentação do modelo oficial de API implementada pelos sistemas criados pela inovação Bild & Vitta.

## Premissas

1. O formato padrão para a troca de dados é o JSON.
1. A extensão do _endpoint_ é opcional para o formato padrão (`.json`) e, quando disponível, pode-se informar a extensão do padrão desejado, como `.xml` ou `.yml`.
1. Os _endpoints_ devem funcionar com ou sem a barra no final do endereço.
1. As chaves de envio e de retorno devem estar no padrão _snake case_.

## Endpoints

- [**`GET`**: `/:model`](#get-model)
- [**`POST`**: `/:model`](#)
- [**`GET`**: `/:model/filters`](#)
- [**`GET`**: `/:model/new`](#)
- [**`GET`**: `/:model/:id`](#)
- [**`PUT`**: `/:model/:id`](#)
- [**`DELETE`**: `/:model/:id`](#)
- [**`GET`**: `/:model/:id/edit`](#)

### `GET`: `/:model`

.

## Respostas

### `status`

Um objeto contendo os detalhes da requisição.

| Chave | Tipo | Obrigatório | Descrição |
|:-:|:-:|:-:|:-|
| `code` | `Number` | Sim | Código do _status_ HTTP da requisição, como `200` ou `404`. |
| `text` | `String` | Não | Mensagem específica contendo detalhes da requisição, geralmente utilizada quando há erros de servidor ou mensagens de sucesso. |
