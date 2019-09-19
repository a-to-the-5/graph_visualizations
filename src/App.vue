<template>
    <div class="box">
      <h1 class="title is-1">Heap</h1>
      <div class="columns">
        <div class="column is-one-third">
          <span class="button is-primary is-fullwidth" @click="insert">
            Insert &nbsp; 
            <input @click.stop="" @keydown.enter="insert" type="number" v-model="numToInsert">
          </span>
        </div>
        <div class="column is-one-third">
          <button class="button is-primary is-fullwidth" @click="remove">Remove {{minMax}}</button>
        </div>
        <div class="column is-one-third">
          <span class="button is-primary is-fullwidth" @click="fill">
            Fill with &nbsp; 
            <input @click.stop="" @keydown.enter="fill" type="number" v-model="fillAmount"> 
            &nbsp; values
          </span>
        </div>
      </div>
      <div class="columns">
        <div class="column is-half">
          <label class="label is-pulled-left">Heap type:</label>&nbsp;&nbsp;
          <input type="radio" id="min" :value="false" v-model="isMax">
          <label for="false"> &nbsp; Min</label>
          &nbsp;&nbsp;
          <input type="radio" id="max" :value="true" v-model="isMax">
          <label for="true"> &nbsp; Max</label>
        </div>
        <div class="column is-half">
          <label class="label is-pulled-left">Animation Delay:</label>&nbsp;&nbsp;
          <input type="range" min="0" max="2000" v-model="delay"/>&nbsp;{{delay}} ms
        </div>
      </div>
      <heap ref="heap" :delay="parseInt(delay)" :max="isMax"></heap>
    </div>
</template>

<script>
import Heap from './components/Heap';

export default {
    data() {
        return {
            numToInsert: 0,
            delay: 200,
            isMax: true,
            extremeInserted: 0,
            fillAmount: 10
        };
    },
    methods: {
        insert() {
            this.$refs.heap.insert(this.numToInsert);
            this.updateValueToInsert();
        },
        remove() {
            this.$refs.heap.pop();
        },
        insertNext() {
          this.$refs.heap.insert(this.extremeInserted);
          this.updateExtreme();
        },
        updateExtreme() {
          if (this.isMax) {
            this.extremeInserted++;
          } else {
            this.extremeInserted--;
          }
        },
        fill() {
          this.$refs.heap.populateHeap(this.fillAmount);
        },
        updateValueToInsert() {
          if(this.isMax) {
            this.numToInsert++;
          } else {
            this.numToInsert--;
          }
        }
    },
    components: {
        'heap': Heap
    },
    watch: {
      isMax: function (value) {
        this.$refs.heap.clear();
        if (value) {
          this.extremeInserted = 0;
          this.numToInsert = 0;
        } else {
          this.extremeInserted = 100;
          this.numToInsert = 100;
        }
      }
    },
    computed: {
      minMax() {
        return this.isMax ? "Max" : "Min";
      }
    }
}
</script>
<style scoped>
@import "~animate.css/animate.css";
.button {
  margin-bottom: 5px;
}
</style>
