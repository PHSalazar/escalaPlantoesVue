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
const eventosPlantoes = reactive([]);

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

            eventosPlantoes.push(novoEventoCalendario);
        })
    }
}
onMounted(() => {
    carregarPlantoesLocalStorage();
});

const handleSubmitModalAddPlantao = (arg) => {

    const novoEvento = {title: arg.nome, start: new Date(arg.start), end: new Date(arg.end), color: arg.color};

    eventosPlantoes.push(novoEvento)
    showModalAddPlantao.value = false;
}

const handleDeleteLocalStorage = () => {
    localStorage.removeItem('plantoesPHS');
    location.reload();
}


</script>





<template>
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