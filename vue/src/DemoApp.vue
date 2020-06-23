<template>
  <div class="demo-app">
    <div class="demo-app-top">
      <button @click="toggleWeekends">toggle weekends</button>
      <button @click="gotoPast">go to a date in the past</button>
      (also, click a date/time to add an event)
    </div>
    <table style="width: 100%">
      <tr v-for="(calendar, index) in calendars" :key="index" class="calendar-master" :data-calendar_id="calendar.id">
        <td><h1>{{ calendar.person }}</h1></td>
        <td style="text-align: center">
          <FullCalendar
            class="demo-app-calendar"
            ref="fullCalendar"
            defaultView="timeGridWeek"
            :header="{
              left: 'prev,next today',
              center: 'title',
              right: 'dayGridMonth,timeGridWeek,timeGridDay,listWeek'
            }"
            :id="calendar.id"
            :plugins="calendarPlugins"
            :editable= "true"
            :weekends="calendarWeekends"
            :events="calendar.events"
            @dateClick="handleDateClick"
          />
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
import $ from "jquery";
import FullCalendar from "@fullcalendar/vue";
import dayGridPlugin from "@fullcalendar/daygrid";
import timeGridPlugin from "@fullcalendar/timegrid";
import interactionPlugin from "@fullcalendar/interaction";

export default {
  components: {
    FullCalendar // make the <FullCalendar> tag available
  },
  data: function() {
    return {
      calendars: [
        { person: "Person 1", 
          id: "calendar1", 
          events: [
            { title: "Event Now", start: new Date() },
            { title: "P1 event 1", start: '2020-06-22 09:00:00', end: '2020-06-22 10:30:00' }
          ] 
        },
        { person: "Person 2", 
          id: "calendar2",
          events: [
            // initial event data
            { title: "P2 event 1", start: '2020-06-23 09:00:00', end: '2020-06-23 10:30:00' },
            { title: "P2 event 2", start: '2020-06-23 14:00:00', end: '2020-06-23 15:00:00' }
          ]
        }
      ],

      calendarPlugins: [
        // plugins must be defined in the JS
        dayGridPlugin,
        timeGridPlugin,
        interactionPlugin // needed for dateClick
      ],
      calendarWeekends: true,
    };
  },
  methods: {
    toggleWeekends() {
      this.calendarWeekends = !this.calendarWeekends; // update a property
    },
    gotoPast() {
      let calendarApi = this.$refs.fullCalendar.getApi(); // from the ref="..."
      calendarApi.gotoDate("2000-01-01"); // call a method on the Calendar object
    },
    handleDateClick(arg) {
      if (confirm("Would you like to add an event to " + arg.dateStr + " ?")) {
        this.calendars[0].events.push({
          // add new event data
          title: "New Event",
          start: arg.date,
          allDay: arg.allDay
        });
      }
    }
  },
  mounted() {
    $('#calendar2 .fc-toolbar').hide()

    $('#calendar1 .fc-dayGridMonth-button').click(function () {
      $('#calendar2 .fc-dayGridMonth-button').click()
    })

    $('#calendar1 .fc-timeGridWeek-button').click(function () {
      $('#calendar2 .fc-timeGridWeek-button').click()
    })

    $('#calendar1 .fc-timeGridDay-button').click(function () {
      $('#calendar2 .fc-timeGridDay-button').click()
    })
  }
};
</script>

<style lang='scss'>
// you must include each plugins' css
// paths prefixed with ~ signify node_modules
@import "~@fullcalendar/core/main.css";
@import "~@fullcalendar/daygrid/main.css";
@import "~@fullcalendar/timegrid/main.css";

.demo-app {
  font-family: Arial, Helvetica Neue, Helvetica, sans-serif;
  font-size: 14px;
}

.demo-app-top {
  margin: 0 0 3em;
}

.demo-app-calendar {
  margin: 0 auto;
  max-width: 900px;
}
</style>
