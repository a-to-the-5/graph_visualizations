<template>
    <div class="box">
      <div class="columns">
        <div class="column is-half">
          <label class="label">Basic ops:</label>
          <span class="button is-primary" @click="insert">
            Insert &nbsp; 
            <input @click.stop="" @keydown.enter="insert" type="number" v-model="numToInsert">
          </span>
          <br>
          <br>
          <button class="button is-primary" @click="remove">Remove {{minMax}}</button>
        </div>
        <div class="column is-half">
          <label class="label">Quick ops:</label>
          <button class="button is-primary" @click="insertNext">Insert Next ({{extremeInserted}})</button>
          <br>
          <br>
          <span class="button is-primary" @click="fill">
            Fill with &nbsp; 
            <input @click.stop="" @keydown.enter="fill" type="number" v-model="fillAmount"> 
            &nbsp; values
          </span>
        </div>
      </div>
      <hr>
      <div class="columns">
        <div class="column is-half">
          <label class="label">Heap type:</label>
          <input type="radio" id="min" :value="false" v-model="isMax">
          <label for="false"> &nbsp; Min</label>
          <br>
          <input type="radio" id="max" :value="true" v-model="isMax">
          <label for="true"> &nbsp; Max</label>
        </div>
        <div class="column is-half">
          <label class="label">Delay:</label>
          <input type="range" min="0" max="2000" v-model="delay"/>{{delay}}
        </div>
      </div>
      <hr>
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
        } else {
          this.extremeInserted = 100;
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

<style>
@import "~animate.css/animate.css";
</style>
