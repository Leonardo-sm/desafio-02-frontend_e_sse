# Desafio 02: Frontend com SSE – Cotação de Ações
Esse repositório faz parte do desafio disponível [nessa thread do twitter](https://twitter.com/zanfranceschi/status/1548344242010869763) e também [nesse post de dev.to](https://dev.to/zanfranceschi/desafio-integracao-com-apis-4jco).

Por favor, note que o código disponível aqui não tem qualidade de produção e não deveria ser usado para referência sobre como desenvolver uma API para SSE.

## Organização do Repositório
- A API para consumir os eventos de cotação está disponível em [src](./src)


## API de Apoio ao Desafio

O restante desse README é dedicado apenas às instruções sobre as APIs de apoio ao desafio. Ou seja, as APIs que você deveria consumir para completar o desafio.

### Docker
Para executar a [imagem docker](https://hub.docker.com/repository/docker/zanfranceschi/desafio-01-cotacoes) diretamente do dockerhub (sem precisar fazer build):
~~~
docker run --rm -p 8080:80 zanfranceschi/desafio-01-cotacoes
~~~

Se preferir fazer o build da imagem, siga os passos à seguir.
~~~
cd ./apis-cotacao/src
~~~

Para construir a imagem docker, execute:
~~~
docker build -t desafio-01-cotacoes .
~~~

Para executar um container, execute:
~~~
docker run --rm -p 8080:80 desafio-01-cotacoes
~~~


### Serviços
Os exemplos têm como premissa que você esteja executando o docker na porta 8080.

#### Serviço A

Requisição:
~~~
GET http://localhost:8080/servico-a/cotacao?moeda=USD
~~~

Resposta:
~~~
HTTP 200
{
  "cotacao": 2.674,
  "moeda": "USD",
  "symbol": "💵"
}
~~~


#### Serviço B

Requisição:
~~~
GET http://localhost:8080/servico-b/cotacao?curr=USD
~~~

Resposta:
~~~
HTTP 200
{
  "cotacao": {
    "fator": 1000,
    "currency": "x",
    "valor": "1468"
  }
}
~~~


#### Serviço C

Requisição:
~~~
POST http://localhost:8080/servico-c/cotacao
{
  "tipo": "EUR",
  "callback": "http://172.17.0.1:3000"
}
~~~

Resposta:
~~~
HTTP 202
{
  "mood": "✅",
  "cid": "74e3fb63-5621-46fd-85d1-56e4e9c04a3a",
  "mensagem": "Quando a cotação finalizar, uma requisição para http://172.17.0.1:3000 será feita."
}
~~~

Requisição do Callback
~~~
POST <URL informada em "callback" da requisição>
{
  "cid": "74e3fb63-5621-46fd-85d1-56e4e9c04a3a",
  "f": 1000,
  "t": "EUR",
  "v": 3.675
}
~~~

## Proposta de Resolução
Uma possível proposta de resolução está disponível em [resolucao-desafio/src](./resolucao-desafio/src)

Para testar, faça a seguinte requisição:
~~~
GET /cotacoes/{moeda}
~~~

A resposta será algo como:
~~~
HTTP 200
{
  "cotacao": 1.689,
  "moeda": "EUR",
  "comparativo": "BRL"
}
~~~

A cotação do Serviço C só é levada em conta se houver um callback em até 5 segundos. Caso contrário, o processamento desiste da cotação desse serviço. Para a comunicação inproc/intra-threads, a biblioteca [NetMQ](https://github.com/zeromq/netmq) foi usada.

## Resoluções da Comunidade

Abaixo você encontra exemplos de resoluções criados pela comunidade:

**JavaScript / Node.js**:
- https://github.com/oieduardorabelo/hexchange - Usando os emissores de eventos [`node:events`](https://nodejs.org/api/events.html#awaiting-multiple-events-emitted-on-processnexttick) do Node.js para aguardar os dados do Serviço C.

**Dart / Shelf**:
- https://github.com/bmsrangel/desafio-01-cotacoes - Usando [Streams](https://api.dart.dev/stable/2.17.3/dart-async/StreamController-class.html) para aguardar os dados do Serviço C.

**Kotlin / Springboot**:
- https://github.com/fabiomaciel/cotacao-service - Usando [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) para aguardar os dados do Serviço C.