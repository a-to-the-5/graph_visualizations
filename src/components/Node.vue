<template>
    <g>
        <template v-if="value != null">
            <g :style="{'transform-origin': cx+'px '+cy+'px'}" 
                 :class="{bubble: inserting, popping: popping}">
                <circle :cx="cx" :cy="cy" r="20" 
                        :stroke="light ? mainColor:highlightColor" 
                        :fill="light ? mainColor:highlightColor"
                        stroke-width="2"
                        />
                <circle :cx="cx+1" :cy="cy+1" r="17" 
                        stroke="white" 
                        fill="white"
                        stroke-width="2"
                        />
                <circle :cx="cx-1" :cy="cy-1" r="18" 
                        :stroke="light ? mainColor:highlightColor" 
                        :fill="light ? mainColor:highlightColor"
                        stroke-width="2"
                        />
                <circle :cx="cx-10" :cy="cy-12" r="2" 
                        stroke="white" 
                        fill="white"
                        stroke-width="2"
                        />
                <circle :cx="cx-14" :cy="cy-6" r="1" 
                        stroke="white" 
                        fill="white"
                        stroke-width="2"
                        />
            </g>
            <text :x="cx" :y="cy" alignment-baseline="middle" 
                  text-anchor="middle" :fill="'#00008B'">
                {{value}}
            </text>
        </template>
    </g>
</template>
<script>
export default {
    props: {
        "value": {
            type: Number
        }, 
        "cx": {
            type: Number
        }, 
        "cy": {
            type: Number
        }, 
        "light": {
            type: Boolean
        }, 
        "inserting": {
            type: Boolean
        }, 
        "popping": {
            type: Boolean
        }, 
        "mainColor": {
            type: String,
            default: "#00FFFF"
        }, 
        "highlightColor": {
            type: String,
            default: "Fuchsia"
        }
    },
    data() {
        return {

        }
    }
}
</script>
<style scoped>
.bubble {
    animation: bubbleIn 0.3s;
}

.popping {
    animation: bubblePop 0.1s;
}

@keyframes bubbleIn {
    0% {
        transform: scale(0.0, 0.0);
    }
    75% {
        transform: scale(1.2, 1.2);
    }
    100% {
        transform: scale(1.0, 1.0);
    }
}

@keyframes bubblePop {
    0% {
        transform: scale(1.0, 1.0);
    }
    90% {
        transform: scale(2.0, 2.0);
        color: white;
    }
    100% {
        transform: scale(0.0, 0.0);
    }
}
</style>