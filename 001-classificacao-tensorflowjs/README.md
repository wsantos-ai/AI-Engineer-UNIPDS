# Classificador de Classe Social

Aplicação web que treina e executa uma rede neural diretamente no navegador para classificar indivíduos em classes sociais (A, B, C, D e E) com base em atributos socioeconômicos. Todo o pipeline de Machine Learning — preparação de dados, treinamento e inferência — ocorre no lado do cliente, sem necessidade de backend ou servidor.

---

## Tecnologias

| Tecnologia | Versão | Uso |
|---|---|---|
| [TensorFlow.js](https://www.tensorflow.org/js) | 4.20.0 (CDN) | Framework de ML no navegador |
| JavaScript (ES6+) | — | Lógica da aplicação e pipeline ML |
| HTML5 / CSS3 | — | Interface responsiva |

---

## Como funciona

### Pipeline de Machine Learning

```
Página carregada
       │
       ▼
┌─────────────────────────────────────────────────────┐
│  TREINAMENTO (automático, ~5–10s)                   │
│  300 amostras rotuladas → normalização → tf.fit()  │
│  150 épocas · Adam · Categorical Cross-Entropy      │
└───────────────────────┬─────────────────────────────┘
                        │ modelo pronto em memória
                        ▼
              Formulário habilitado
                        │
              usuário preenche 6 campos
                        │
                        ▼
┌─────────────────────────────────────────────────────┐
│  PRÉ-PROCESSAMENTO                                  │
│  Numéricos → normalização (÷ máximo da feature)     │
│  Categóricos → one-hot encoding                     │
│  Resultado: vetor de 19 dimensões                   │
└───────────────────────┬─────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────┐
│  INFERÊNCIA (model.predict)                         │
│  Dense 128 (ReLU) → Dense 64 (ReLU) → Dense 5      │
│  Softmax → distribuição de probabilidades           │
└───────────────────────┬─────────────────────────────┘
                        │
                        ▼
         Classe prevista + confiança (%)
         Barras de probabilidade (A–E)
```

### Arquitetura da Rede Neural

| Camada | Neurônios | Ativação |
|---|---|---|
| Entrada | 19 | — |
| Dense 1 | 128 | ReLU |
| Dense 2 | 64 | ReLU |
| Saída | 5 | Softmax |

- **Otimizador:** Adam  
- **Função de perda:** Categorical Cross-Entropy  
- **Épocas:** 150  
- **Amostras de treino:** 300 (embaralhadas a cada época)

### Features de entrada

Seis atributos socioeconômicos são coletados e transformados em um vetor de **19 dimensões**:

| Feature | Tipo | Encoding | Dimensões |
|---|---|---|---|
| Idade | Numérico | `valor / 68` | 1 |
| Renda per capita (R$) | Numérico | `valor / 14.927,95` | 1 |
| Tamanho da família | Numérico | `valor / 9` | 1 |
| Escolaridade | Categórico | One-hot (7 níveis) | 7 |
| Região | Categórico | One-hot (5 regiões) | 5 |
| Ocupação | Categórico | One-hot (4 tipos) | 4 |

**Níveis de Escolaridade:** Sem instrução · Ensino Fundamental · Ensino Médio · Graduação · Pós-graduação · Mestrado · Doutorado  
**Regiões:** Sudeste · Sul · Centro-Oeste · Nordeste · Norte  
**Ocupações:** CLT · Autônomo · Aposentado · Desempregado

### Classes de saída

| Classe | Denominação |
|---|---|
| A | Alta |
| B | Média-Alta |
| C | Média |
| D | Média-Baixa |
| E | Baixa |

---

## Estrutura do projeto

```
001-classificacao-tensorflowjs/
├── index.html      # Interface com formulário de entrada e exibição de resultados
├── index.js        # Pipeline completo: dataset, treinamento, inferência e eventos
├── style.css       # Estilos responsivos com feedback visual por classe
├── package.json    # Metadados do projeto e dependência do TensorFlow.js
└── README.md
```

---

## Instalação e uso

### Pré-requisitos

- Navegador moderno com suporte a WebGL (Chrome 90+, Firefox 88+, Edge 90+)
- Node.js (opcional, apenas para servir os arquivos localmente)

### Executar localmente

1. Clone o repositório:
   ```bash
   git clone https://github.com/me-wsantos/AI-Engineer-UNIPDS.git
   cd AI-Engineer-UNIPDS/001-classificacao-tensorflowjs
   ```

2. Abra diretamente no navegador:
   ```bash
   # Opção A — sem servidor (pode ter restrições CORS em alguns browsers)
   open index.html

   # Opção B — com servidor HTTP local (recomendado)
   npx serve .
   
   ```

3. Aguarde o treinamento do modelo (~5–10 segundos) e preencha os campos do formulário.

---

## Detalhes de implementação

- **Execução no navegador:** o TensorFlow.js é carregado via CDN e utiliza aceleração WebGL quando disponível, sem necessidade de instalação local da biblioteca.
- **Dados embutidos:** o dataset com 300 amostras está hardcoded em `index.js`, eliminando dependências de APIs externas.
- **Gerenciamento de memória:** tensores são descartados com `tensor.dispose()` após a inferência para evitar vazamentos de memória.
- **Estado do modelo:** o modelo treinado persiste em memória durante a sessão e é descartado ao recarregar a página (não há persistência via `tf.LayersModel.save`).

---

## Autor

<a href="https://github.com/me-wsantos">
  <img style="border-radius: 50%;" src="https://avatars.githubusercontent.com/u/179779189?v=4" width="80px;" alt="Wellington Santos"/>
</a>

**Wellington Santos**

[![LinkedIn](https://img.shields.io/badge/-Wellington--Santos-blue?style=flat-square&logo=Linkedin&logoColor=white)](https://www.linkedin.com/in/wellington-lima-dos-santos-13343143/)
[![Email](https://img.shields.io/badge/-me@wellington--santos.com-c14438?style=flat-square&logo=Gmail&color=11ab3a&logoColor=white)](mailto:me@wellington-santos.com)
