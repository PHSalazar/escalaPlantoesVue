<script setup>
import ListaDePlantoes from './ListaDePlantoes/ListaDePlantoes.vue';

const dados = defineProps(['pessoa']);
const emit = defineEmits(['closeModalDetalhes']);

let numeroFinaisDeSemana = 0;
let diasFinaisDeSemana = [];
let totalDiasPlantoes = 0;

const closeModal = (element) => {
    if (!element.target.closest('.modalContent') || element.target.className === 'buttonCloseModal') {
        emit('closeModalDetalhes');
    }else{
        console.log(element.target.className)
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

        
        

        novaLista.map(({nome, start, end}) => {
            totalDiasPlantoes += days_between(start, end);
           numeroFinaisDeSemana += (contarFinaisDeSemana(start, end));
        });

        let frase;
        (totalDiasPlantoes > 1) ? frase = `${totalDiasPlantoes} plantões` : frase = `${totalDiasPlantoes} plantão`;

        return frase;

    }
}


const contarFinaisDeSemana = (dataInicio, dataFinal) => {
    let dates = [];
    let finaisDeSemana = 0;

    let currentDate = new Date(dataInicio);
    const endDate = new Date(dataFinal);

    while (currentDate < endDate) {
        dates.push(new Date(currentDate));
        
        if (currentDate.getDay() == 0 || currentDate.getDay() == 6) {
            // 0 DOmingo, 6 Sabado
            finaisDeSemana += 1;
            diasFinaisDeSemana.push(currentDate.toLocaleDateString('pt-BR'));
        }

        currentDate.setDate(currentDate.getDate() + 1);
    }



    return finaisDeSemana;
}

function days_between(date1, date2) {

    const second = 1000;
    const minute = second * 60;
    const hour = minute * 60;
    const day = hour * 24;

    let date_ini = new Date(date1);
    let date_end = new Date(date2);

    let diff = date_end.getTime() - date_ini.getTime();

    return Math.floor(diff / day);
}

</script>
<template>
    <div class="modal" @click="closeModal">
        <div class="modalContent">
            <h2>Detalhes</h2>
            <p style="padding-top: 10px;"><b>{{ dados.pessoa.nome }}</b> tem {{ contarPlantoes(dados.pessoa.nome) }} no mês de <b>{{ getMonth(dados.pessoa.start) }}</b>, dos {{ totalDiasPlantoes }} plantões, <b>{{ diasFinaisDeSemana.length }}</b>  {{ diasFinaisDeSemana.length === 1 ? 'cai Final' : 'caem Finais' }}  de Semana.</p>

            <ListaDePlantoes
            :nome="dados.pessoa.nome"
            :mes="dados.pessoa.start"
            :fds="diasFinaisDeSemana"
            :diasTotal="totalDiasPlantoes"
            />

            <button class="buttonCloseModal" @click="closeModal">Fechar detalhes</button>
        </div>
    </div>
</template>


<style>
.buttonCloseModal {
            padding: 10px;
            background: none;
            border: 0;
            outline: none;

            background: #CC3827;
            border-radius: 20px;
            color: #fff;

            display: block;
            margin: 10px auto;

            font-size: 1em;

            cursor: pointer;

            &:hover {
                opacity: .8;
            }
        
            position: absolute;
            top: 20px;
            right: 40px;
        }


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
        width: calc(100vw - 40px);
        height: calc(100vh - 40px);
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