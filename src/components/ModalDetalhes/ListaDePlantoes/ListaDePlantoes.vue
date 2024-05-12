<script setup>
const dados = defineProps(['nome', 'mes', 'fds', 'diasTotal']);
import { ref, onMounted } from 'vue';

const numeroPlantoes = ref([]);
const datasPlantoes = ref([]);

const getMonth = (date) => {
    const newDate = new Date(date);
    const months = ['Janeiro', 'Fevereiro', 'Março', 'Abril', 'Maio', 'Junho', 'Julho', 'Agosto', 'Setembro', 'Outubro', 'Novembro', 'Dezembro'];
    return months[newDate.getMonth()];
}

const buscarPlantoesPorNome = (nomePessoa) => {
    const plantoes = localStorage.getItem("plantoesPHS");

    if (plantoes) {
        const plantoesJSON = JSON.parse(plantoes);
        const mesAtual = getMonth(dados.mes);

        const novaLista = plantoesJSON.filter(user => user.nome.toLowerCase() === nomePessoa.toLowerCase() && mesAtual === getMonth(user.start));
        novaLista.map(plantao => {
            // datasPlantoes.value.push(formatDate(plantao.start));
            datasPlantoes.value.push(plantao);
        })

        datasPlantoes.value.sort();

    }
}

const formatDateReduce = (date, numberToReduce) => {
    const newDate = new Date(date);
    newDate.setDate(newDate.getDate() - numberToReduce);

    let dd = newDate.getDate();
    let mm = newDate.getMonth() + 1;
    const yyyy = newDate.getFullYear();

    dd < 10 ? dd = '0' + dd : dd;
    mm < 10 ? mm = '0' + mm : mm;

    const dateFullFormatted = `${dd}/${mm}/${yyyy}`;

    return dateFullFormatted;
}

const deletarPlantao = (dado) => {
    const plantoes = localStorage.getItem("plantoesPHS");
    if (plantoes) {
        let plantoesJSON = JSON.parse(plantoes);

        plantoesJSON = plantoesJSON.filter(plantao => {
            return (
                plantao.nome !== dado.nome ||
                plantao.start !== dado.start ||
                plantao.end !== dado.end ||
                plantao.color !== dado.color
            );
        });
        localStorage.setItem("plantoesPHS", JSON.stringify(plantoesJSON));
        location.reload();
    }
}



onMounted(() => {
    buscarPlantoesPorNome(dados.nome);
})

</script>

<template>
    <div class="wrapper">
        <div class="listContainer">
            <h4>Todos os Plantões ({{ dados.diasTotal }})</h4>

            <table>
                <thead>
                    <th>Início</th>
                    <th>Final</th>
                    <th>Ações</th>
                </thead>
                <tr v-for="dataPlantao in datasPlantoes" class="itemDataPlantao">
                    <th>
                        {{ new Date(dataPlantao.start).toLocaleDateString('pt-BR') }}
                    </th>
                    <th>
                        {{ formatDateReduce(dataPlantao.end, 1) }}
                    </th>
                    <th>
                        <button class="btnAction">
                            <img src="../../../assets/icoEdit.svg" alt="Ícone Editar" width="16px" height="16px">
                        </button>
                        <button class="btnAction" @click='deletarPlantao(dataPlantao)'>
                            <img src="../../../assets/icoDelete.svg" alt="Ícone delete" width="16px" height="16px">
                        </button>
                    </th>
                </tr>

            </table>

            <hr>

            <h4>Finais de Semana ({{ dados.fds.length }})</h4>

            <p v-if="dados.fds.length === 0" class="noLegend">
                Nenhum plantão de {{ dados.nome }} cai no final de semana.
            </p>
            <table v-else >
                    <thead>
                        <th>Data</th>
                    </thead>
                    <tbody>
                        <tr v-for="dataPlantaoFinalSemana in dados.fds" class="itemDataPlantao">
                            <th >
                                {{ dataPlantaoFinalSemana }}
                            </th>
                        </tr>
                    </tbody>
            </table>

           
        </div>
    </div>
</template>

<style scoped>
h4 {
    color: var(--color-blue);
}
.wrapper {
    height: 290px;
    overflow-y: auto;
}
.listContainer {
    margin-top: 20px;
    padding: 0 20px;
}
.itemDataPlantao {
    font-family: 'Poppins', sans-serif;
    color: var(--color-blue);
    padding: 8px 0;
}
table {
    width: 100%;
    border: 0;
    border-collapse: collapse;
}
thead {
    background-color: var(--color-green);
    color: #fff;
    height: 30px;
}
th {
    font-weight: 400;
    border-bottom: 1px solid #e5e5e5;
    height: 30px !important;

    &:last-of-type {
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 10px;
    }
}
tr:nth-child(odd) {
    background-color: #f5f5f5;
}

.btnAction {
    height: 25px;
    width: 25px;
    display: flex;
    align-items: center;
    justify-content: center;
}

hr {
    margin: 10px;
}
.noLegend {
    text-align: center;
}
</style>