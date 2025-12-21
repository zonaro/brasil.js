# cidades_brasil.js

Biblioteca JavaScript com dados completos de todas as cidades e estados do Brasil, incluindo informações geográficas, códigos IBGE, CEPs e muito mais.

## Instalação

### Via CDN (Recomendado para uso rápido)

Inclua o script diretamente no seu HTML:

```html
<script src="https://cdn.jsdelivr.net/gh/zonaro/cidades_brasil.js@master/cidades_brasil.js"></script>
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
- `siafi`: Sistema integrado de Administração Financeira
- `microRegiao`: Microrregião
- `macroRegiao`: Macrorregião
- `exclusivaSedeUrbana`: Booleano indicando se é sede urbana exclusiva
- `codigoGeograficoSubdivisao`: Código geográfico da subdivisão
- `codigoGeograficoDistrito`: Código geográfico do distrito

## Funções Disponíveis ✅

A biblioteca expõe várias funções úteis no objeto global `window.brasil`. Abaixo está a documentação das funções públicas (as funções que começam com `__` são internas e não documentadas aqui).

### `pesquisarCidade(param, threshold)` 🔎

- **Descrição:** Busca cidades por nome, CEP, código IBGE (cidade ou estado), UF ou região. Quando não encontra uma correspondência direta, tenta buscar pelo nome do estado e, em último caso, aplica busca *fuzzy* (Levenshtein) com limite definido por `threshold`.
- **Parâmetros:**
  - `param` (string|number) — Texto ou número para busca (nome da cidade, CEP de 8 dígitos, código IBGE, UF, etc.).
  - `threshold` (number, opcional, padrão `3`) — Distância máxima de Levenshtein para comparação *fuzzy*.
- **Retorno:** `Array` de objetos cidade (pode ser vazio se nada for encontrado).
- **Exemplo:**
```javascript
const cidades = brasil.pesquisarCidade('São');
const porCep = brasil.pesquisarCidade(20000000); // busca por CEP
```

### `pegarCidade(param)` 👉

- **Descrição:** Retorna a primeira cidade encontrada usando a mesma lógica de busca de `pesquisarCidade` ou `null` se não houver resultados.
- **Parâmetros:** `param` (string|number) — Mesmo formato de `pesquisarCidade`.
- **Retorno:** objeto cidade ou `null`.
- **Exemplo:**
```javascript
const cidade = brasil.pegarCidade('Rio de Janeiro');
if (cidade) console.log(cidade.uf, cidade.regiao);
```

### `pesquisarEstado(param, threshold)` 🗺️

- **Descrição:** Busca estados por nome, região, UF ou código IBGE (2 dígitos). Se não houver correspondência direta, aplica busca *fuzzy* com o `threshold` informado.
- **Parâmetros:**
  - `param` (string|number) — Nome do estado, UF ou código IBGE (2 dígitos).
  - `threshold` (number, opcional) — Limite para busca *fuzzy* (padrão `3`).
- **Retorno:** `Array` de objetos estado.
- **Exemplo:**
```javascript
const estados = brasil.pesquisarEstado('SP');
```

### `pegarEstado(param)` 👉

- **Descrição:** Retorna o primeiro estado correspondente à busca ou `null` se nada for encontrado.
- **Parâmetros:** `param` (string|number)
- **Retorno:** objeto estado ou `null`.

### `cidadeProxima(lat, lng)` 📍

- **Descrição:** Calcula a cidade mais próxima a partir de coordenadas (usa a fórmula de Haversine para distância em km).
- **Parâmetros:**
  - `lat` (number|string) — Latitude.
  - `lng` (number|string) — Longitude.
- **Retorno:** objeto cidade mais próxima.
- **Exemplo:**
```javascript
const proxima = brasil.cidadeProxima(-22.9068, -43.1729);
```

### `aqui()` 🌐

- **Descrição:** Função assíncrona que obtém a localização do usuário via Geolocation API e retorna a cidade mais próxima.
- **Parâmetros:** nenhum
- **Retorno:** `Promise` que resolve em um objeto cidade ou `null`. A promise é rejeitada se a geolocalização não for suportada ou o usuário negar a permissão.
- **Exemplo:**
```javascript
brasil.aqui().then(cidade => { /* ... */ }).catch(err => { /* ... */ });
```

### `googleMapsUrl(cidade)` 🔗

- **Descrição:** Gera uma URL do Google Maps para as coordenadas da cidade (`https://maps.google.com/maps?q=lat,long`). Retorna `null` se latitude/longitude não estiverem disponíveis.
- **Parâmetros:** `cidade` (objeto cidade com `latitude` e `longitude`)
- **Retorno:** `string` URL ou `null`.

### `googleMapsEmbedded(cidade, width, height)` 🧭

- **Descrição:** Gera um elemento `<iframe>` pronto para inserir no DOM com o mapa embutido (embed). `width` e `height` têm valores padrão de `600x450` se não fornecidos.
- **Parâmetros:**
  - `cidade` (objeto cidade)
  - `width` (number, opcional)
  - `height` (number, opcional)
- **Retorno:** `HTMLIFrameElement` ou `null` se coordenadas forem inválidas.
- **Exemplo:**
```javascript
const iframe = brasil.googleMapsEmbedded(cidade, 600, 400);
document.getElementById('mapa').appendChild(iframe);
```

**Propriedades úteis:**

- `brasil.cidades` — `Array` com todas as cidades.
- `brasil.estados` — `Array` com os estados (gerado automaticamente a partir de `cidades`).
- `brasil.regioes` — `Array` com as regiões sem duplicatas.

> ⚠️ Observação: Funções internas que começam com `__` (por exemplo `__levenshtein`, `__normalize`) são privadas e não fazem parte da API pública documentada aqui.


### Exemplos

#### Buscar cidades por nome

```javascript
// Buscar cidades que contenham "São" no nome
const cidadesSao = brasil.pesquisarCidade('São');
console.log(`Encontradas ${cidadesSao.length} cidades com "São" no nome.`);

// Pegar a primeira cidade encontrada
const primeiraCidade = brasil.pegarCidade('Rio de Janeiro');
if (primeiraCidade) {
    console.log(`Rio de Janeiro: ${primeiraCidade.uf}, ${primeiraCidade.regiao}`);
}
```

#### Buscar cidade por código IBGE

```javascript
// Buscar cidade pelo código IBGE de 7 dígitos
const cidadeIBGE = brasil.pegarCidade(3304557); // Rio de Janeiro
if (cidadeIBGE) {
    console.log(`${cidadeIBGE.nome} - ${cidadeIBGE.estado}`);
}
```

#### Buscar cidades por CEP

```javascript
// Buscar cidades que incluem um CEP específico
const cidadesCEP = brasil.pesquisarCidade(20000000); // CEP do Rio de Janeiro
cidadesCEP.forEach(cidade => {
    console.log(`${cidade.nome} - CEP de ${cidade.cepInicial} a ${cidade.cepFinal}`);
});
```

#### Encontrar cidade mais próxima por coordenadas

```javascript
// Encontrar a cidade mais próxima de uma latitude e longitude
const cidadeProxima = brasil.cidadeProxima(-22.9068, -43.1729); // Coordenadas do Rio
if (cidadeProxima) {
    console.log(`Cidade mais próxima: ${cidadeProxima.nome}, ${cidadeProxima.uf}`);
}
```

#### Obter localização atual e cidade

```javascript
// Obter a cidade baseada na localização atual (requer permissão de geolocalização)
brasil.aqui().then(cidade => {
    if (cidade) {
        console.log(`Você está em ${cidade.nome}, ${cidade.estado}`);
    } else {
        console.log('Não foi possível determinar a localização');
    }
}).catch(error => {
    console.error('Erro ao obter localização:', error);
});
```

#### Gerar URL do Google Maps para uma cidade

```javascript
const cidade = brasil.pegarCidade('Brasília');
if (cidade) {
    const url = brasil.googleMapsUrl(cidade);
    console.log(`Link para o mapa: ${url}`);
}
```

#### Estados

A biblioteca também fornece uma lista de estados:

```javascript
console.log(`Há ${brasil.estados.length} estados no Brasil.`);

```


#### Buscar estados (`pesquisarEstado` / `pegarEstado`)

```javascript
// Buscar por UF
const estadosSP = brasil.pesquisarEstado('SP');
console.log(estadosSP);

// Buscar por código IBGE de 2 dígitos (ex: 35 para São Paulo)
const estadosByIbge = brasil.pesquisarEstado(35);
console.log(estadosByIbge);

// Pegar o primeiro resultado (ou null)
const primeiroEstado = brasil.pegarEstado('SP');
console.log(primeiroEstado);

// Busca fuzzy: caso o nome esteja escrito com erro, ajustar threshold
const fuzzy = brasil.pesquisarEstado('Sao Paulo', 2);
console.log(fuzzy);
```

#### Regiões e listas úteis

```javascript
console.log('Regiões:', brasil.regioes); // lista de regiões sem duplicatas
console.log('Total de cidades:', brasil.cidades.length);
```

#### Gerar IFrame do Google Maps (`googleMapsEmbedded`)

```javascript
const brasilia = brasil.pegarCidade('Brasília');
if (brasilia) {
    // Retorna um HTMLIFrameElement pronto para inserir no DOM
    const iframe = brasil.googleMapsEmbedded(brasilia, 800, 600);
    if (iframe) document.getElementById('mapa-container').appendChild(iframe);
}
```


## Exemplo Completo com Select2

Aqui está um exemplo completo de uma página HTML usando Select2 para buscar cidades dinamicamente com a função pesquisarCidade, exibindo informações e mapa embedded ao selecionar.

Veja o exemplo em ação: [exemplo.html](exemplo.html)

```html
<!DOCTYPE html>
<html lang="pt-br">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Exemplo cidades_brasil.js com Select2</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <!-- Select2 CSS -->
    <link href="https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/css/select2.min.css" rel="stylesheet">
    <!-- cidades_brasil.js -->
    <script src="https://cdn.jsdelivr.net/npm/cidades_brasil.js@latest/cidades_brasil.js"></script>
</head>

<body>
    <div class="container mt-5">
        <h1>Busca de Cidades do Brasil</h1>
        <div class="mb-3">
            <label for="cidade-select" class="form-label">Selecione uma cidade:</label>
            <select id="cidade-select" style="width: 100%;"></select>
            <button id="local-btn" class="btn btn-primary mt-2">Usar Minha Localização</button>
        </div>
        <div id="cidade-card" class="card" style="display: none;">
            <div class="card-body">
                <h5 class="card-title" id="cidade-nome"></h5>
                <p class="card-text" id="cidade-info"></p>
                <div id="mapa-container"></div>
            </div>
        </div>
    </div>

    <!-- jQuery -->
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <!-- Select2 JS -->
    <script src="https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/js/select2.min.js"></script>
    <script>
        $(document).ready(function () {
            // Inicializar Select2 com busca dinâmica usando pesquisarCidade
            $('#cidade-select').select2({
                ajax: {
                    transport: function (params, success, failure) {
                        const term = params.data.term || '';
                        // Pesquisa cidade com busca fuzzy de até 3 caracteres
                        const results = window.brasil.pesquisarCidade(term, 3);
                        success({
                            results: results.map(c => ({ id: c.ibge, text: c.nome + ' - ' + c.uf }))
                        });
                    },
                    processResults: function (data) {
                        return data;
                    }
                },
                placeholder: 'Digite o nome da cidade...',
                allowClear: true,
                minimumInputLength: 1
            });

            // Evento de seleção
            $('#cidade-select').on('select2:select', function (e) {
                const ibge = e.params.data.id;
                const cidade = window.brasil.pegarCidade(ibge);
                if (cidade) {
                    $('#cidade-nome').text(cidade.nome);
                    $('#cidade-info').html(`
                        <strong>UF:</strong> ${cidade.uf}<br>
                        <strong>Estado:</strong> ${cidade.estado}<br>
                        <strong>Região:</strong> ${cidade.regiao}<br>
                        <strong>IBGE:</strong> ${cidade.ibge}<br>
                        <strong>DDD:</strong> ${cidade.ddd}<br>
                        <strong>Latitude:</strong> ${cidade.latitude}<br>
                        <strong>Longitude:</strong> ${cidade.longitude}<br>
                        <strong>Altitude:</strong> ${cidade.altitude} m<br>
                        <strong>CEP Inicial:</strong> ${cidade.cepInicial}<br>
                        <strong>CEP Final:</strong> ${cidade.cepFinal}
                    `);
                    const mapa = window.brasil.googleMapsEmbedded(cidade, 600, 400);
                    $('#mapa-container').empty().append(mapa);
                    $('#cidade-card').show();
                }
            });

            // Evento de limpar
            $('#cidade-select').on('select2:clear', function () {
                $('#cidade-card').hide();
            });

            // Evento do botão de localização
            $('#local-btn').on('click', function () {
                window.brasil.aqui().then(cidade => {
                    if (cidade) {
                        $('#cidade-select').select2('data', { id: cidade.ibge, text: cidade.nome + ' - ' + cidade.uf });
                        $('#cidade-nome').text(cidade.nome);
                        $('#cidade-info').html(`
                            <strong>UF:</strong> ${cidade.uf}<br>
                            <strong>Estado:</strong> ${cidade.estado}<br>
                            <strong>Região:</strong> ${cidade.regiao}<br>
                            <strong>IBGE:</strong> ${cidade.ibge}<br>
                            <strong>DDD:</strong> ${cidade.ddd}<br>
                            <strong>Latitude:</strong> ${cidade.latitude}<br>
                            <strong>Longitude:</strong> ${cidade.longitude}<br>
                            <strong>Altitude:</strong> ${cidade.altitude} m<br>
                            <strong>CEP Inicial:</strong> ${cidade.cepInicial}<br>
                            <strong>CEP Final:</strong> ${cidade.cepFinal}
                        `);
                        const mapa = window.brasil.googleMapsEmbedded(cidade, 600, 400);
                        $('#mapa-container').empty().append(mapa);
                        $('#cidade-card').show();
                    } else {
                        alert('Não foi possível determinar a localização');
                    }
                }).catch(error => {
                    console.error('Erro ao obter localização:', error);
                    alert('Erro ao obter localização');
                });
            });
    </script>
</body>

</html>
```

Este exemplo cria um select dropdown com cidades agrupadas por UF, permite busca e seleção, e exibe um card com detalhes da cidade selecionada, incluindo um mapa embedded.


## Contribuição

Contribuições são bem-vindas! Abra uma issue ou envie um pull request no [GitHub](https://github.com/zonaro/cidades_brasil.js).

## Licença

Veja o arquivo LICENSE para detalhes.
