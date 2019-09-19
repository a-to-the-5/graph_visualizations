<template>
    <div class="box" width="80%">
        <svg height="800" :width="width">
            <edge v-for="(value, index) in filteredElements.slice(1)" :key="'edge'+index+'.'+value"
                  :source="[cx(parentIndex(index+1)), cy(parentIndex(index+1))]"
                  :dest="[cx(index+1), cy(index+1)]"
                  :isInserting="isInserting(index+1)"
                  :isPopping="isPopping(index+1)"
                  :isLeft="index%2 == 0"></edge>
            <node v-for="(value, index) in filteredElements" :key="'node'+index+'.'+value" 
                  :value="value" :cx="cx(index)" :cy="cy(index)"
                  :light="isLight(index)"
                  :inserting="isInserting(index)"
                  :popping="isPopping(index)"></node>
        </svg>

    </div>
</template>

<script>
import Node from './Node';
import Edge from './Edge';
import { setTimeout } from 'timers';
import { Promise } from 'q';

export default {
    props: {
        'delay': {
            type: Number,
            default: 200
        }, 
        'max': {
            type: Boolean,
            default: true
        }
    },
    data() {
        return {
            elements: [],
            beingSwapedIndexes: [],
            levelHeight: 80,
            width: 800,
            popped: [],
            inserting: false,
            popping: false,
            busy: false
        }
    },
    methods: {
        clear() {
            this.elements = [];
        },
        populateHeap(numElements=10) {
            this.elements = [];
            for (let i = 0; i < numElements; i++) {
                this.elements.push(i);
            }
            if(this.max) {
                this.elements = this.elements.reverse();
            }
        },
        async insert(value) {
            if (this.busy) return;
            this.busy = true;
            this.inserting = true;
            this.elements.push(parseInt(value));
            this.$forceUpdate();
            await this.wait(300);
            this.inserting = false;
            await this.wait(100);
            await this.heapifyUp(this.elements.length-1);
        },
        async pop() {
            if (this.busy) return;
            this.busy = true;
            await this.swap(0, this.elements.length-1, false);
            await this.wait(100);
            this.beingSwapedIndexes = [];
            this.popping = true;
            this.$forceUpdate();
            await this.wait(120);
            var element = this.elements.pop();
            this.popping = false;
            this.popped.push(element);
            await this.wait(100);
            await this.heapifyDown(0);
        },
        async heapifyUp(index) {
            if (this.hasParent(index) && this.oppositeCompare(this.parent(index), this.elements[index])) {
                await this.swap(index, this.parentIndex(index));
                await this.wait(this.delay/2);
                this.beingSwapedIndexes = [];
                await this.heapifyUp(this.parentIndex(index));
            }
            this.busy = false;
        },
        async heapifyDown(index) {
            if (this.hasLeftChild(index)) {
                var maxChildIndex = this.leftChildIndex(index);
                if (this.hasRightChild(index) && this.compare(this.rightChild(index), this.leftChild(index))) {
                    maxChildIndex = this.rightChildIndex(index);
                }
                if (this.compare(this.elements[maxChildIndex], this.elements[index])) {
                    await this.swap(index, maxChildIndex);
                    await this.wait(this.delay/2);
                    this.beingSwapedIndexes = [];
                    await this.heapifyDown(maxChildIndex);
                }
            }
            this.busy = false;
        },
        compare(val1, val2) {
            if (this.max) {
                return val1 > val2;
            } else {
                return val1 < val2;
            }
        },
        oppositeCompare(val1, val2) {
            if (this.max) {
                return val1 < val2;
            } else {
                return val1 > val2;
            }
        },
        async swap(index1, index2, bubbling=true) {
            if (bubbling) {
                this.beingSwapedIndexes = [index1];
            } else {
                this.beingSwapedIndexes = [index1, index2];
            }
            this.$forceUpdate();
            await this.wait(this.delay/2);
            var temp = this.elements[index1]
            this.elements[index1] = this.elements[index2];
            this.elements[index2] = temp;
            if (bubbling) {
                this.beingSwapedIndexes = [index2];
            } else {
                this.beingSwapedIndexes = [index1, index2];
            }
            this.$forceUpdate();
        },
        getLevel(index) {
            return Math.floor(Math.log2(index+1));
        },
        numNodesInLevel(level) {
            return Math.pow(2, level);
        },
        nodeWidth(index) {
            return this.width / this.numNodesInLevel(this.getLevel(index));
        },
        nodeIndexInLevel(index) {
            return index - this.numNodesInLevel(this.getLevel(index)) + 1;
        },
        leftChildIndex(index) { 
            return index * 2 + 1; 
        },
        rightChildIndex(index) { 
            return index * 2 + 2; 
        },
        parentIndex(index) { 
            return Math.floor((index - 1) / 2);
        },
        hasLeftChild(index) { 
            return this.leftChildIndex(index) < this.elements.length;
        },
        hasRightChild(index) { 
            return this.rightChildIndex(index) < this.elements.length;
        },
        hasParent(index) { 
            return this.parentIndex(index) >= 0; 
        },
        leftChild(index) {
            return this.elements[this.leftChildIndex(index)];
        },
        rightChild(index) {
            return this.elements[this.rightChildIndex(index)];
        },
        parent(index) {
            return this.elements[this.parentIndex(index)];
        },
        startX(index) {
            return this.nodeIndexInLevel(index) * this.nodeWidth(index);
        },
        endX(index) {
            return (this.nodeIndexInLevel(index) + 1) * this.nodeWidth(index);
        },
        startY(index) {
            return this.getLevel(index) * this.levelHeight;
        },
        cx(index) {
            return (this.startX(index) + this.endX(index)) / 2;
        },
        cy(index) {
            return this.startY(index) + 25;
        },
        isLight(index) {
            if (this.beingSwapedIndexes.includes(index)) {
                return false;
            }
            return true;
        },
        wait(ms){
            return new Promise(function(resolve, reject){
                setTimeout(function(){
                    resolve(ms); 
                    reject(ms);
                },ms);
            });
        },
        getClass(index) {
            if (this.inserting && index == this.elements.length-1) {
                return 'animated fadeIn';
            } else {
                return '';
            }
        },
        isInserting(index) {
            return this.inserting && index == this.elements.length-1;
        },
        isPopping(index) {
            return this.popping && index == this.elements.length-1;
        }
    },
    components: {
        "node": Node,
        "edge": Edge
    },
    computed: {
        filteredElements() {
            var self = this;
            return this.elements.map((element, index) => {
                return self.beingSwapedIndexes.includes(index) ? element : element
            });
        }
    }
}
</script>

<style>

</style>
