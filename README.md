# brasil.js

Biblioteca JavaScript com dados completos de todas as cidades e estados do Brasil, incluindo informações geográficas, códigos IBGE, CEPs e muito mais.

## Instalação

### Via CDN (Recomendado para uso rápido)

Inclua o script diretamente no seu HTML:

```html
<script src="https://cdn.jsdelivr.net/npm/brasil.js@latest/brasil.js"></script>
```

Isso carregará a biblioteca globalmente, tornando o objeto `window.brasil` disponível.

### Estrutura dos Dados

A biblioteca fornece um objeto com a seguinte estrutura:

- `brasil.cidades`: Array de objetos representando todas as cidades do Brasil.

Cada cidade possui as seguintes propriedades:

- `nome`: Nome da cidade
- `cepInicial` e `cepFinal`: Faixa de CEPs
- `latitude` e `longitude`: Coordenadas geográficas
- `uf`: Sigla do estado
- `estado`: Nome completo do estado
- `regiao`: Região do Brasil (Norte, Nordeste, Centro-Oeste, Sudeste, Sul)
- `ibge`: Código IBGE da cidade
- `ibgeEstado`: Código IBGE do estado
- `ddd`: Código de área telefônica
- `capital`: Booleano indicando se é capital
- `altitude`: Altitude em metros
- `timeZone`: Fuso horário
- E outras informações como SIAFI, microregião, etc.

### Exemplos

#### Listar todas as cidades de um estado

```javascript
// Filtrar cidades de São Paulo
const cidadesSP = brasil.cidades.filter(cidade => cidade.uf === 'SP');
console.log(`São Paulo tem ${cidadesSP.length} cidades.`);

// Exemplo: Abadia de Goiás
console.log(cidadesSP[0]);
```

#### Encontrar uma cidade pelo nome

```javascript
const cidade = brasil.cidades.find(c => c.nome.toLowerCase() === 'rio de janeiro');
if (cidade) {
    console.log(`Rio de Janeiro está na região ${cidade.regiao}, DDD ${cidade.ddd}`);
}
```

#### Obter cidades por região

```javascript
const cidadesNordeste = brasil.cidades.filter(c => c.regiao === 'Nordeste');
console.log(`O Nordeste tem ${cidadesNordeste.length} cidades.`);
```

#### Calcular estatísticas

```javascript
// Contar capitais
const capitais = brasil.cidades.filter(c => c.capital);
console.log(`Há ${capitais.length} capitais no Brasil.`);

// Média de altitude das cidades
const mediaAltitude = brasil.cidades.reduce((sum, c) => sum + c.altitude, 0) / brasil.cidades.length;
console.log(`Altitude média: ${mediaAltitude.toFixed(2)} metros.`);
```

### Estados

Além das cidades, você pode derivar informações sobre estados agrupando os dados:

```javascript
// Agrupar cidades por estado
const estados = {};
brasil.cidades.forEach(cidade => {
    if (!estados[cidade.uf]) {
        estados[cidade.uf] = {
            nome: cidade.estado,
            regiao: cidade.regiao,
            cidades: []
        };
    }
    estados[cidade.uf].cidades.push(cidade);
});

console.log(Object.keys(estados).length + ' estados carregados.');
```

## Contribuição

Contribuições são bem-vindas! Abra uma issue ou envie um pull request no [GitHub](https://github.com/zonaro/brasil.js).

## Licença

Veja o arquivo LICENSE para detalhes.
