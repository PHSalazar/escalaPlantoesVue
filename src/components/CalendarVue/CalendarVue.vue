<script setup>
import FullCalendar from '@fullcalendar/vue3'
import dayGridPlugin from '@fullcalendar/daygrid'
import interactionPlugin  from '@fullcalendar/interaction'
import ModalAddPlantao from '../ModalAddPlantao/ModalAddPlantao.vue'
import ModalDetalhes from '../ModalDetalhes/ModalDetalhes.vue'

import { onMounted, reactive, ref } from 'vue';

const showModalAddPlantao = ref(false);
const showModalDetalhes = ref(false);

const selectionDateStart = ref(null);
const selectionDateEnd = ref(null);
let eventosPlantoes = reactive([]);
const nomesPlantonistas = reactive([]);

const clickedEventTitle = ref('');

const calendarOptions = ref({
    plugins: [dayGridPlugin, interactionPlugin],
    initialView: 'dayGridMonth',
    weekends: true,
    locales: 'ptLocales',
    eventDisplay: 'block',
    selectable: true,
    displayEventTime: false,
    showNonCurrentDates: false,
    fixedWeekCount: false,
    longPressDelay: 1,
    select: function({start, end}) {
        selectionDateStart.value = start;
        selectionDateEnd.value = end;

        showModalAddPlantao.value = true;
    },
    eventClick: function(info) {
        // alert(info.event.title);
        clickedEventTitle.value = {nome: info.event.title, start: info.event.start};
        showModalDetalhes.value = true;
    },
    events: eventosPlantoes,
    height: 500,
    headerToolbar: {
        start: 'title',
        center: '',
        end: 'prev,next'
    }
})

const handleCloseModalAddPlantao = () => {
    showModalAddPlantao.value = false;
}

const handleCloseModalDetahes = () => {
    showModalDetalhes.value = false;
}


const filtrarPlantoes = (element) => {
    const nomePlantonista = element.target.value;
    eventosPlantoes.splice(0);

    const plantoesLocalStorage = localStorage.getItem("plantoesPHS");
    if (plantoesLocalStorage) {
        let plantoesJSON = JSON.parse(plantoesLocalStorage);
        plantoesJSON.map(({nome, start, end, color}) => {
            const novoEventoCalendario = {
                title: nome,
                start: start,
                end: end,
                color: color
            }

            if (novoEventoCalendario.title === nomePlantonista) {
                eventosPlantoes.push(novoEventoCalendario);
            }else if (nomePlantonista === 'todos') {
                eventosPlantoes.push(novoEventoCalendario);
            }
        })
    }
    
}

const carregarPlantoesLocalStorage = () => {
    const plantoesLocalStorage = localStorage.getItem("plantoesPHS");
    if (plantoesLocalStorage) {
        let plantoesJSON = JSON.parse(plantoesLocalStorage);
        plantoesJSON.map(({nome, start, end, color}) => {
            const novoEventoCalendario = {
                title: nome,
                start: start,
                end: end,
                color: color
            }
            nomesPlantonistas.indexOf(novoEventoCalendario.title) === -1 && nomesPlantonistas.push(novoEventoCalendario.title);

            eventosPlantoes.push(novoEventoCalendario);
        })
    }
}

onMounted(() => {
    carregarPlantoesLocalStorage();
});

const handleSubmitModalAddPlantao = (arg) => {

    const novoEvento = {title: arg.nome, start: new Date(arg.start), end: new Date(arg.end), color: arg.color};

    nomesPlantonistas.indexOf(novoEvento.title) === -1 && nomesPlantonistas.push(novoEvento.title);


    eventosPlantoes.push(novoEvento)
    showModalAddPlantao.value = false;
}

const handleDeleteLocalStorage = () => {
    localStorage.removeItem('plantoesPHS');
    location.reload();
}


</script>





<template>

    <p class="filterContainer">
        Mostrar plantões de:
        <select :onchange="filtrarPlantoes">
        <option value="todos">Todos</option>
        <option v-for="plantonista in nomesPlantonistas" :value="plantonista">{{ plantonista }}</option>
        </select>

    </p>
    <div class="calendar">
        <FullCalendar :options="calendarOptions" />
    </div>

    <template v-if="showModalAddPlantao">
        <ModalAddPlantao 
        :interval-date-start="selectionDateStart" 
        :interval-date-end="selectionDateEnd" 
        @onSomeEvent="handleCloseModalAddPlantao"
        @submit="handleSubmitModalAddPlantao"
        />
    </template>


    <template v-if="showModalDetalhes">
        <ModalDetalhes
        :pessoa="clickedEventTitle"
        @closeModalDetalhes="handleCloseModalDetahes"
        />
    </template>

    <button @click="handleDeleteLocalStorage">
        Apagar escala COMPLETA
    </button>
</template>








<style>
    .filterContainer {
        font-family: 'Poppins', sans-serif !important;
        font-weight: 400;
        text-align: center;
        margin: 10px auto;
    }
    .calendar {
        width: 90vw;
        height: 500px;
        margin: 20px auto;
    }
    .fc .fc-daygrid-day-bottom {
        background-color: #CC3827 !important;
    }

    .fc-toolbar-title,
    .fc-daygrid-day-number {
        font-family: 'Poppins', sans-serif !important;
        font-weight: 300;
        color: var(--color-blue);
        
        &::first-letter {
            text-transform: capitalize;
        }
    }

    .fc-col-header-cell-cushion {
        color: var(--color-blue);
        font-family: 'Poppins', sans-serif !important;
        font-weight: 400;
        
        &::first-letter {
            text-transform: capitalize;
        }

    }

    .fc-col-header-cell:first-of-type *,
    .fc-col-header-cell:last-of-type * {
        color: #CC3827;
    }

    tr[role='row'] {
        td:first-of-type,
        td:last-of-type {
            .fc-daygrid-day-number {
                color: #CC3827;
            }
        }
    }
</style>