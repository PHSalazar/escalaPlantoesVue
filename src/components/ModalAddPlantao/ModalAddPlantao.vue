<script setup>
import { onMounted, reactive, ref } from 'vue';

const { intervalDateStart, intervalDateEnd } = defineProps(['intervalDateStart', 'intervalDateEnd']);
const pessoasCarregadas = reactive([]);
const nomeProfissional = ref('');

const emit = defineEmits(['onSomeEvent', 'submit'])

const formatDate = (date) => {
    const newDate = new Date(date);
    let dd = date.getDate();
    let mm = date.getMonth() + 1;
    const yyyy = date.getFullYear();

    dd < 10 ? dd = '0' + dd : dd;
    mm < 10 ? mm = '0' + mm : mm;

    const dateFullFormated = `${dd}/${mm}/${yyyy}`;

    return dateFullFormated;
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

const handleCloseModalEmmit = (element) => {
    if (!element.target.closest('.modalContent') || element.target.value === "Cancelar") {
        emit('onSomeEvent');
    }
}

const obterItemChaveProfissional = (nomeProcurado, valorProcurado) => {
    const plantoes = localStorage.getItem('plantoesPHS');
    if (plantoes) {
        const userName = JSON.parse(plantoes);
        let obj = userName.find(o => o.nome.toLowerCase() === nomeProcurado.toLowerCase());

        // Encontrou o nomeProcurado
        if (obj) {
            const valorEncontrado = obj.color;
            return valorEncontrado
        }else{
            // Não encontrou esse usuário, então gerar nova cor para ele
            const colors = ['#26547C', '#EF476F', '#5C0029', '#06D6A0', '#7F6A93', '#FC814A', '#D282A6', '#80A1D4', '#FFBF46', '#4E5340'];
            let colorRandom;
            // Verificar se existe outro nome com a mesma cor a fim de não repetir a cor
            do {
                colorRandom = colors[Math.floor(Math.random() * colors.length)];
            } while (userName.some(o => o.color === colorRandom));

            return colorRandom;
        }
    }else{
        const colors = ['#26547C', '#EF476F', '#5C0029', '#06D6A0', '#7F6A93', '#FC814A', '#D282A6', '#80A1D4', '#FFBF46', '#4E5340'];
        const colorRandom = colors[Math.floor(Math.random() * colors.length)];
        return colorRandom;
    }
}

const handleAddPlantonistaCalendario = (event) => {
    event.preventDefault();
    let nomeProfissionalTrim = nomeProfissional.value.trim();

    if (nomeProfissional.value.trim() != '') {
        const color = obterItemChaveProfissional(nomeProfissionalTrim, "color");

        //Salvando nova escala no localstorage
        const plantoes = localStorage.getItem('plantoesPHS');
        

        if (plantoes) {
            let plantoesArray = JSON.parse(plantoes);
            
            const escala = {
                nome: nomeProfissionalTrim, 
                start: intervalDateStart, 
                end: intervalDateEnd, 
                color
            };

            plantoesArray = [
                ...plantoesArray,
                escala
            ];
            
            localStorage.setItem('plantoesPHS', JSON.stringify(plantoesArray));

        }else{
            const escala = [{
                nome: nomeProfissionalTrim, 
                start: intervalDateStart, 
                end: intervalDateEnd, 
                color
            }];
            

            localStorage.setItem('plantoesPHS', JSON.stringify(escala));
        }
        let incluirEscala = {};
        incluirEscala = {
            nome: nomeProfissionalTrim, 
            start: intervalDateStart, 
            end: intervalDateEnd, 
            color
        };

        emit('submit', incluirEscala);   
    }
}

const carregarPlantoesLocalStorage = () => {
    const plantoesLocalStorage = localStorage.getItem("plantoesPHS");
    if (plantoesLocalStorage) {
        const plantoesJSON = JSON.parse(plantoesLocalStorage);

        plantoesJSON.map(({nome}) => {
            pessoasCarregadas.push(nome);
        });
    }
};
onMounted(() => {
    carregarPlantoesLocalStorage();
});

</script>

<template>
    <div class="modal" @click="handleCloseModalEmmit">
        <div class="modalContent">
            <h2 class="poppins-light">Adicionar Plantão</h2>

            <p class="poppins-light">Quem está de plantão do dia <b>{{ formatDate(intervalDateStart) }}</b> ao <b>{{ formatDateReduce(intervalDateEnd, 1) }}</b>?</p>

            <form @submit="handleAddPlantonistaCalendario">
                <input type="text" name="nomeProfissional" id="nomeProfissional" placeholder="Nome do Profissional que está sendo escalado" class="poppins-light" v-model="nomeProfissional"/>

                <!-- <select name="nomeProfissionalSelect" id="nomeProfissionalSelect">
                    <option value="" v-for="item in pessoasCarregadas">{{ item }}</option>
                </select> -->
                <div class="footer">
                    <input type="button" value="Cancelar" @click="handleCloseModalEmmit"/>
                    <input type="submit" value="Adicionar plantão ao Calendário" />
                </div>
            </form>
        </div>
    </div>
</template>

<style scoped>
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

        h2 {
            padding: 20px 0 0 20px;
            color: var(--color-blue);
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
}

p {
    padding: 15px 20px;
    font-size: 0.85em;
}

input#nomeProfissional {
    padding: 10px;
    background: none;
    outline: none;

    border: 1px solid var(--color-blue);
    border-radius: 20px;

    width: 90%;
    display: block;
    margin: 10px auto;

    font-size: 1em;

    &::placeholder {
        color: var(--color-blue);
    }
}



select#nomeProfissionalSelect {
    padding: 10px;
    background: none;
    outline: none;

    border: 1px solid var(--color-blue);
    border-radius: 20px;

    width: 90%;
    display: block;
    margin: 10px auto;

    font-size: 1em;
    color: var(--color-blue)
}

@media (max-width: 600px) {
    .modalContent {
        height: auto !important;
        padding-bottom: 20px;
    }
}
</style>