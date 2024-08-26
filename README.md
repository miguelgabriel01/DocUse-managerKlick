### Tutorial: Implementando Rastreamento de Cliques em um Projeto Vue.js

## Introdução

Neste tutorial, vamos implementar o rastreamento de cliques em um projeto Vue.js usando a classe `ClickTracker` para gerenciar e enviar dados de cliques para uma API desenvolvida em NestJS.

### Pré-requisitos

Antes de começar, você precisará clonar o repositório da API e iniciar o servidor:

1. **Clone o repositório da API NestJS:**

   ```bash
   git clone https://git.com/teste/tesre](https://github.com/miguelgabriel01/MG-ClickStream-Middleware
   ```

2. **Instale as dependências e inicie a API:**

   Navegue até a pasta do projeto clonado e execute os comandos:

   ```bash
   cd tesre
   npm install
   npm run start
   ```

Isso iniciará a API NestJS na porta `3001` por padrão. Agora, podemos implementar o rastreamento de cliques no nosso projeto Vue.js e enviar os dados para essa API.

### Passo 1: Implementar a Classe `ClickTracker`

Crie uma classe `ClickTracker` que será responsável por gerenciar e enviar os dados dos cliques para a API.

**src/ClickTracker.js**

```javascript
import axios from 'axios';

class ClickTracker {
  constructor(endpoint, sendInterval = 10000) {
    this.endpoint = endpoint;
    this.sendInterval = sendInterval;
    this.migalhaDePao = {
      data: null,
      hora: null,
      caminhoPecorrido: []
    };
    this.timeoutId = null;
  }

  trackButtonClick(buttonId) {
    this.migalhaDePao.caminhoPecorrido.push(buttonId);
    if (!this.migalhaDePao.data) {
      const now = new Date();
      this.migalhaDePao.data = now.toLocaleDateString('pt-BR');
      this.migalhaDePao.hora = now.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit', second: '2-digit' });

      this.startTimeout();
    }
  }

  startTimeout() {
    if (this.timeoutId) {
      clearTimeout(this.timeoutId);
    }
    this.timeoutId = setTimeout(() => {
      this.sendData();
    }, this.sendInterval);
  }

  sendData() {
    const migalhaJson = JSON.stringify(this.migalhaDePao, null, 2);
    console.log(migalhaJson);

    axios.post(this.endpoint, this.migalhaDePao)
      .then(response => {
        console.log('Dados enviados com sucesso:', response.data);
        this.resetData();
      })
      .catch(error => {
        console.error('Erro ao enviar os dados:', error);
      });
  }

  resetData() {
    this.migalhaDePao = {
      data: null,
      hora: null,
      caminhoPecorrido: []
    };
  }
}

export default ClickTracker;
```

### Passo 2: Criar um Componente Vue.js Simples

Vamos criar um componente Vue.js com três botões, cada um com um ID seguindo a convenção de nomenclatura, e configurá-los para enviar dados de cliques para a API.

**src/components/ClickTrackerComponent.vue**

```html
<template>
  <div>
    <h1>Exemplo de Rastreamento de Cliques</h1>

    <button :id="'CS-simple-action-Button1'" @click="handleClick('CS-simple-action-Button1')">Botão 1</button>
    <button :id="'CS-simple-action-Button2'" @click="handleClick('CS-simple-action-Button2')">Botão 2</button>
    <button :id="'CS-simple-action-Button3'" @click="handleClick('CS-simple-action-Button3')">Botão 3</button>
  </div>
</template>

<script>
import ClickTracker from '../ClickTracker';

export default {
  name: 'ClickTrackerComponent',
  data() {
    return {
      clickTracker: new ClickTracker('http://localhost:3001/save') // URL da API NestJS
    };
  },
  methods: {
    handleClick(buttonId) {
      this.clickTracker.trackButtonClick(buttonId);
    }
  }
}
</script>

<style scoped>
button {
  background: rgb(7, 141, 47);
  color: aliceblue;
  font-size: 15px;
  width: 180px;
  height: 40px;
  text-decoration: none;
  border: none;
  border-radius: 2px;
  margin: 10px;
}
</style>
```

### Passo 3: Aplicar a Lógica de Nomenclatura para IDs de Botões

Para garantir uma nomenclatura consistente e clara para os botões em seus projetos, recomendamos a seguinte convenção:

#### Estrutura da Nomenclatura

A estrutura recomendada para os IDs dos botões segue o padrão:

**`CS + [Seção] + [Ação/Opção]`**

- **`CS`**: Prefixo que representa o contexto do projeto, onde `CS` significa "ClickStream" no seu caso.
- **`Seção`**: Identifica a seção do HTML onde o botão está localizado, como `nav` (barra de navegação), `footer` (rodapé), `form` (formulário), etc.
- **`Ação/Opção`**: Descreve a ação que o botão realiza ou a opção que ele representa, como `salvar`, `excluir`, `deletar`, `copiar`, `sim`, `não`, `jogar`, etc.

### Passo 4: Testar a Funcionalidade

1. **Inicie a API NestJS**:

   Se você ainda não fez isso, certifique-se de que a API está rodando na porta correta (`3030`).

   ```bash
   cd tesre
   npm run start
   ```

2. **Execute o projeto Vue.js**:

   ```bash
   npm run serve
   ```

3. **Abra o navegador e acesse o aplicativo**. Clique nos botões e verifique se os dados dos cliques estão sendo enviados para a API NestJS.

4. **Verifique o terminal da API** para confirmar que os dados dos cliques estão sendo recebidos e processados corretamente.
