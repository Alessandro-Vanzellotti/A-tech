<template>
    <button v-on:click="disparaAçao()" class="botão botão-perigo" :type="tipo"> {{ rotulo }} </button>
    
</template>

<script>

export default {

    props: ['tipo', 'rotulo', 'confirmacao', 'estilo'],

    methods: {
        disparaAçao() {
           if(this.confirmacao) {

               if(confirm('Confirma operação?')) {
                    this.$emit('botaoAtivado');
               }
               return;
           }
           this.$emit('botaoAtivado');
        }
    }
}

</script>

<style scoped>
    .botao {
        display: inline-block;
        padding: 10px;
        border-radius: 3px;
        margin: 10px;
        font-size: 1.2em;
    }

    .botao-perigo {
        background: firebrick;
        color: white;
    }

    .botao-padrao {
        background: darkcyan;
        color: white;
    }
</style>
