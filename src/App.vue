<template>
  <div id="container">
    
    <div id="introdução">
      <h1>Cadastro de candidatos para a aliança:</h1>
      <h3>Bem-vindos, cadetes! Aqui, começa a jornada de vocês junto à Aliança. Para tanto, preencha os campos a seguir:</h3>
    </div>
    
    <div id="inputs">
      <form @submit.prevent="grava()">
        <label for="nome_cadete">Nome do Cadete:</label>
        <input v-model="cadete.name" type="text" id="nome_cadete" name="nome_cadete" placeholder="e.g. Anakin Skywalker" autocomplete="off" required autofocus><br><br>
        <label for="planeta_cadete">Planeta de Origem</label>
        <input v-model="cadete.planet" type="text" id="planeta_cadete" name="planeta_cadete" autocomplete="off" placeholder="e.g. Dorlo" required><br><br>
        <label for="nascimento_cadete">Data de Nascimento:</label>
        <input v-model="cadete.birthDate" type="text" id="nascimento_cadete" name="nascimento_cadete" placeholder="dd/mm/aaaa"
          pattern="^(?:(?:31(\/|-|\.)(?:0?[13578]|1[02]))\1|(?:(?:29|30)(\/|-|\.)(?:0?[13-9]|1[0-2])\2))(?:(?:1[6-9]|[2-9]\d)?\d{2})$|^
          (?:29(\/|-|\.)0?2\3(?:(?:(?:1[6-9]|[2-9]\d)?(?:0[48]|[2468][048]|[13579][26])|(?:(?:16|[2468][048]|[3579][26])00))))$|^(?:0?[
          1-9]|1\d|2[0-8])(\/|-|\.)(?:(?:0?[1-9])|(?:1[0-2]))\4(?:(?:1[6-9]|[2-9]\d)?\d{2})$" required><br><br>
        <label for="motivação_cadete">Motivação para se juntar à Aliança:<br> <i>(max 100 caracteres)</i></label><br><br>
        <textarea v-model="cadete.description" name="motivação_cadete" rows="10" cols="40" maxlength="100"></textarea><br><br>
        <input type="submit" value="Submit">
      </form>
    </div>

    <div id="dados-devolvidos">
      <input type="search" class="filtro" v-on:input="filtro = $event.target.value" placeholder="filtre pelo nome">
      <p v-show="mensagem"><b>{{ mensagem }}</b></p>
      <table id="tabela">

        <thead>
          <tr>
            <th>Id</th>
            <th>Nome</th>
            <th>Planeta</th>
            <th>Data de Nascimento</th>
            <th>Descrição da motivação para entrar na aliança</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="cadete of cadetesComFiltro">
            <td> {{ cadete.id }} </td>
            <td> {{ cadete.name }}</td>
            <td> {{ cadete.planet }}</td>
            <td> {{ cadete.birthDate }} </td>
            <td> {{ cadete.description }} </td>
            <td class="ações"><meu-botao 
              rotulo="Remover" 
              tipo="button" 
              v-on:botaoAtivado="remove(cadete)"
              :confirmacao="true"
              estilo="padrão"
              /></td> 

          </tr>
        </tbody>

      </table>
    </div>
    
    
    
    
  </div>
</template>

<script>

import Botao from './components/shared/botao/Botao.vue';
import Cadete from './domain/cadete/Cadete';

export default {

    components: {
      'meu-botao': Botao
    },

    data() {
      return {
        cadetes: [],
        filtro: '',
        cadete: new Cadete(),
        mensagem: ''

        }
        

      },

  computed: {

    cadetesComFiltro() {
      if (this.filtro) {
        let exp = new RegExp(this.filtro.trim(), 'i');
        return this.cadetes.filter(cadete => exp.test(cadete.name));
      } else {
        return this.cadetes;
      }
    }
  },

    methods: {
      
      remove(cadete) {
       this.$http
        .delete(`https://test-mais-a-educacao-v1.herokuapp.com/${cadete.id}?token=alessandroDeOliveiraVanzellottiMonteiro`)
        .then(() => {
            this.mensagem = 'Cadete removido da lista com sucesso'
          }, 
          err => {
            this.mensagem = 'Não foi possível remover o cadete da lista';
            console.log(err);})
            .then(() => this.$http.get('https://test-mais-a-educacao-v1.herokuapp.com?token=alessandroDeOliveiraVanzellottiMonteiro'))
            .then(res => res.json())
            .then(cadetes => this.cadetes = cadetes, err => console.log(err));
      },

      grava() {
        this.$http
        .post('https://test-mais-a-educacao-v1.herokuapp.com?token=alessandroDeOliveiraVanzellottiMonteiro', this.cadete)
        .then(() => this.cadete = new Cadete(), err => console.log(err))
        .then(() => this.$http.get('https://test-mais-a-educacao-v1.herokuapp.com?token=alessandroDeOliveiraVanzellottiMonteiro'))
        .then(res => res.json())
        .then(cadetes => this.cadetes = cadetes, err => console.log(err));
      }
    },


    created() {

        this.$http.get('https://test-mais-a-educacao-v1.herokuapp.com?token=alessandroDeOliveiraVanzellottiMonteiro')
        .then(res => res.json())
        .then(cadetes => this.cadetes = cadetes, err => console.log(err));
    }
}
</script>

<style>
  :root {
    background-color: rgb(36, 29, 29);
  }

  #introdução {
    margin: 1%;
    color: white;

    text-align: center;
  }

  #inputs {
    
    padding: 1% 0 1% 1%;
    margin: 1% auto;
    border-radius: 25px;
    
    width: 25%;

    background-color: rgba(255, 255, 255, 0.849);
  }

  #inputs textarea {
    width: 95%;

  }

  .filtro {
    width: 100%;
    margin: 1% 0;
  }

  #dados-devolvidos {
    display: block;
    padding: 0 1%;
    border-radius: 20px;

    background-color: rgba(255, 255, 255, 0.849);
  }

  #tabela {
    padding: 1%;

    margin-bottom: 1%;
  }

  #tabela th, td {
    border-style: groove;
    height: 40%;
    width: inherit;
  }

  .ações {
    border-style: none;
  }

</style>
