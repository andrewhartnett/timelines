<template>
  <div id="app">
    <!-- Events -->
    <div class="baseEvent" v-for="event in dates">
      <timeline v-show="event.visible" 
      :start="event.start" 
      :end="event.end" 
      :title="event.title" 
      :yearPercent="eachYearPercent"
      :color="event.color"
      ></timeline>
    </div>

    <!-- One Line That is the timeline -->
    <div class="baseTimeline">
      <div>{{startYear}}</div>
      <div>{{endYear}}</div>
    </div>

    <div id="eventEditor" class="flex-between">

      <div>
        <div>
          <strong>Timeline</strong>
          <div>
              <input type="text" v-model="startYear"><input type="text" v-model="endYear">
          </div>
        </div>

        <strong>Events</strong> <a href="#" @click.prevent="clear">Clear</a>
        
        <draggable v-model="dates">
          <div v-for="(event, i) in dates">
            <input type="text" v-model="event.title">
            <input type="text" v-model="event.start">
            <input type="text" v-model="event.end">
            <input type="color" v-model="event.color">
            <input type="checkbox" v-model="event.visible">
            <button @click.prevent="removeEvent(i)">x</button>
          </div>
        </draggable>
        
        <div style="margin-top: 15px">
          <input type="text" v-model="newTitle" placeholder="Title">
          <input type="text" v-model="newStart" placeholder="Start Year">
          <input type="text" v-model="newEnd" placeholder="End Year">
          <input type="color" v-model="newColor">
          <button @click.prevent="newEvent">+</button>
        </div>

        <div style="margin-top: 20px">
          <input type="text" placeholder="Label" v-model="timelineLabel"><button @click.prevent="saveTimeline">Save</button>
        </div>
      </div>
      <div>
        <strong>Suggestions</strong>
        <div>
          <a v-for="suggestion in suggestions" href="#" @click.prevent="showSuggestion(suggestion)">{{suggestion.title}}</a>
        </div>
        <strong>My Timelines</strong>
        <div v-for="(timeline, i) in timelines">
          <a href="#" @click.prevent="showSuggestion(timeline)">{{timeline.label}}</a> <a href="#" @click.prevent="deleteTimeline(i)">x</a>
        </div>
      </div>
    </div>
      

  </div>
</template>

<script>
import Timeline from './components/Timeline.vue'
import draggable from 'vuedraggable'
import toastr from 'toastr'
import suggestions from './mixins/suggestions'

export default {
  name: 'App',
  mixins: [suggestions],
  data() {
    return {
      timelineLabel: '',
      startYear: 1990,
      endYear: 2020,
      dates: [],
      newTitle: '',
      newStart: '',
      newEnd: '',
      newColor: '#a30031',
      timelines: []
    }
  },
  methods: {
    newEvent(){
      this.dates.push({start: this.newStart, end: this.newEnd, title: this.newTitle, color: this.newColor, visible: true});
      this.newStart = '';
      this.newEnd = '';
      this.newTitle = '';
      this.newColor = '';
    },
    saveTimeline() {
      const data = {
        label: this.timelineLabel,
        start: this.startYear,
        end: this.endYear,
        dates: this.dates,
      }

      if(this.timelines){
        this.timelines.push(data);
      }else{
        this.timelines = [data];
      }

      localStorage.setItem('my-timelines', JSON.stringify(this.timelines));
      toastr.success('Saved');
    },
    getTimelines() {
      const data = JSON.parse(localStorage.getItem('my-timelines'));
      if(data){
        this.timelines = data;
      }
    },
    removeEvent(index){
      this.dates.splice(index, 1);
    },
    deleteTimeline(index){
      this.timelines.splice(index, 1);
      localStorage.setItem('my-timelines', JSON.stringify(this.timelines));
      toastr.success('Timeline Removed');
    },
    showSuggestion(suggestion){
      this.startYear = suggestion.start;
      this.endYear = suggestion.end;
      this.dates = suggestion.dates;
    },
    clear() {
      this.dates = [];
    }
  },
  computed: {
    eachYearPercent() {
      return parseFloat(Number(1/(this.endYear-this.startYear) * 100).toFixed(2));
    }
  },
  components: {
    Timeline,
    draggable
  },
  mounted(){
    this.getTimelines();
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin-top: 60px;
}

#eventEditor{
  margin: 20px 0;
}

.baseTimeline{
  display: flex;
  justify-content: space-between;
  width: 100%;
  border-bottom: 2px solid #000;
}

.baseEvent{
  display: flex;
  justify-content: space-between;
  width: 100%;
  margin-bottom: 5px;
}

.flex-between{
  display: flex;
  justify-content: space-between;
}
</style>
