# brasil.js

Biblioteca JavaScript com dados completos de todas as cidades e estados do Brasil, incluindo informações geográficas, códigos IBGE, CEPs e muito mais.

## Instalação

### Via CDN (Recomendado para uso rápido)

Inclua o script diretamente no seu HTML:

```html
<script src="https://cdn.jsdelivr.net/gh/zonaro/brasil.js@master/brasil.js"></script>
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

// Encontrar um estado por UF
const estadoSP = brasil.estados.find(e => e.uf === 'SP');
if (estadoSP) {
    console.log(`Estado: ${estadoSP.nome}, UF: ${estadoSP.uf}`);
}
```

## Exemplo Completo com Select2

Aqui está um exemplo completo de uma página HTML usando Select2 para buscar cidades dinamicamente com a função pesquisarCidade, exibindo informações e mapa embedded ao selecionar.

```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Exemplo Brasil.js com Select2</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <!-- Select2 CSS -->
    <link href="https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/css/select2.min.css" rel="stylesheet">
    <!-- Brasil.js -->
    <script src="https://cdn.jsdelivr.net/npm/brasil.js@latest/brasil.js"></script>
</head>
<body>
    <div class="container mt-5">
        <h1>Busca de Cidades do Brasil</h1>
        <div class="mb-3">
            <label for="cidade-select" class="form-label">Selecione uma cidade:</label>
            <select id="cidade-select" style="width: 100%;"></select>
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
        $(document).ready(function() {
            // Inicializar Select2 com busca dinâmica usando pesquisarCidade
            $('#cidade-select').select2({
                ajax: {
                    transport: function (params, success, failure) {
                        const term = params.data.term || '';
                        const results = window.brasil.pesquisarCidade(term);
                        success({
                            results: results.map(c => ({ id: c.ibge, text: c.nome }))
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
            $('#cidade-select').on('select2:select', function(e) {
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
            $('#cidade-select').on('select2:clear', function() {
                $('#cidade-card').hide();
            });
        });
    </script>
</body>
</html>
```

Este exemplo cria um select dropdown com cidades agrupadas por UF, permite busca e seleção, e exibe um card com detalhes da cidade selecionada, incluindo um mapa embedded.

## Contribuição

Contribuições são bem-vindas! Abra uma issue ou envie um pull request no [GitHub](https://github.com/zonaro/brasil.js).

## Licença

Veja o arquivo LICENSE para detalhes.
