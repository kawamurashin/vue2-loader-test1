<template>
  <v-app>
    <draggable
        v-model="tables"
        group="people"
        @start="drag=true"
        @end="drag=false"
        item-key="id"
        v-bind="dragOptions"
        handle=".handle">

        <div v-for="element in tables" :key="element.id" class="list-group-item">
          <span class="handle">
            <v-icon
                large
                color="black"
                style="margin: 10px">
              mdi-drag-horizontal-variant
            </v-icon>
          </span>
          <span class="text">{{ element.name }} </span>
        </div>
    </draggable>
    <div>await name : {{await_name}}</div>
    <div>event name : {{event_name}}</div>
    <v-main>
      <HelloWorld/>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld';
import draggable from 'vuedraggable'
import {ModelManager} from "@/scripts/model/modelManager.js";
import {CommonEvent} from "@/scripts/events/commonEvent.js";
export default {
  name: 'App',
  components: {
    HelloWorld,
    draggable
  },
  mounted() {
    const loadComplete = (e) =>
    {
      this.event_name = e.data.data.name

    }
    const modelManager = ModelManager.getInstance();
    modelManager.addEventListener(CommonEvent.LOAD_COMPLETE, loadComplete)
    modelManager.load().then(result =>
    {
      this.await_name = result.data.data.name;
      this.tables = result.data.data.tables;

    })
    modelManager.intervalLoad();
  },

  data: () => ({
    drag:false,
    tables:[],
    await_name:"",
    event_name:""
  }),
  computed: {
    dragOptions() {
      return {
        animation: 200,
        group: "description",
        disabled: false,
        ghostClass: "ghost"
      };
    }
  },
};
</script>

<style>
.ghost {
  opacity: 0.75;
  background: #c8ebfb;
}
.list-group-item {
  padding: 5px;
  border-bottom: solid #000000 1px;
}
.handle {
  cursor: move;
}
</style>
