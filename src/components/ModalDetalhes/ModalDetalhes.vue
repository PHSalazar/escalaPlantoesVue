<script setup>
import ListaDePlantoes from './ListaDePlantoes/ListaDePlantoes.vue';

const dados = defineProps(['pessoa']);
const emit = defineEmits(['closeModalDetalhes']);

const closeModal = (element) => {
    if (!element.target.closest('.modalContent')) {
        emit('closeModalDetalhes');
    }
}

const getMonth = (date) => {
    const newDate = new Date(date);
    const months = ['Janeiro', 'Fevereiro', 'Março', 'Abril', 'Maio', 'Junho', 'Julho', 'Agosto', 'Setembro', 'Outubro', 'Novembro', 'Dezembro'];
    return months[newDate.getMonth()];
}

const contarPlantoes = (nomePessoa) => {
    const plantoes = localStorage.getItem("plantoesPHS");

    if (plantoes) {
        const plantoesJSON = JSON.parse(plantoes);
        const mesEvento = getMonth(dados.pessoa.start);

        const novaLista = plantoesJSON.filter(user => user.nome.toLowerCase() === nomePessoa.toLowerCase() && mesEvento === getMonth(user.start));
        let frase;
        (novaLista.length > 1) ? frase = `${novaLista.length} plantões` : frase = `${novaLista.length} plantão`;

        return frase;

    }
}
</script>
<template>
    <div class="modal" @click="closeModal">
        <div class="modalContent">
            <h2>Detalhes</h2>
            <p style="padding-top: 10px;"><b>{{ dados.pessoa.nome }}</b> tem {{ contarPlantoes(dados.pessoa.nome) }} no mês de <b>{{ getMonth(dados.pessoa.start) }}</b>.</p>

            <ListaDePlantoes
            :nome="dados.pessoa.nome"
            :mes="dados.pessoa.start"
            />
        </div>
    </div>
</template>


<style>
.modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 9999;

    .modalContent {
        background: #fff;
        width: 500px;
        height: 400px;
        padding: 0 15px;

        h2 {
            padding: 20px 0 0 20px;
            color: var(--color-blue);

            border-bottom: 1px solid var(--color-blue);
        }
    }

    .footer {
        display: flex;
        align-items: center;
        justify-content: center;


        input[type='button'],
        input[type='submit'] {
            padding: 10px;
            background: none;
            border: 0;
            outline: none;

            background: var(--color-green);
            border-radius: 20px;
            color: #fff;

            display: block;
            margin: 10px auto;

            font-size: 1em;

            cursor: pointer;

            &:hover {
                opacity: .8;
            }
        }
        input[value='Cancelar'] {
            background: #CC3827;
            font-weight: bold
        }
        
    }

    p {
        font-weight: 300;
        font-family: 'Open Sans', sans-serif;
    }
    b {
        color: var(--color-blue);
    }
}
</style>