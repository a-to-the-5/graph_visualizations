<template>
    <path stroke="blue" stroke-width="1" stroke-linecap="round" fill="transparent"
          :class="{line: isInserting, lineShrinking: isPopping}"
          :style="{'transform-origin': source[0]+'px '+source[1]+'px'}"
          :d="curve"/>
</template>
<script>
export default {
    props: {
        source: {
            type: Array
        },
        dest: {
            type: Array
        },
        isInserting: {
            type: Boolean
        },
        isPopping: {
            type: Boolean
        },
        isLeft: {
            type: Boolean
        }
    },
    computed: {
        curve() {
            var p1x = this.source[0];
            var p1y = this.source[1];
            var p2x = this.dest[0];
            var p2y = this.dest[1];
            var mpx = (p2x + p1x) * 0.5;
            var mpy = (p2y + p1y) * 0.5;

            // angle of perpendicular to line:
            var theta = Math.atan2(p2y - p1y, p2x - p1x) - Math.PI / 2;

            // distance of control point from mid-point of line:
            var offset = this.isLeft ? -30 : 30;

            // location of control point:
            var c1x = mpx + offset * Math.cos(theta);
            var c1y = mpy + offset * Math.sin(theta);
            return "M" + p1x + " " + p1y + " Q " + c1x + " " + c1y + " " + p2x + " " + p2y;
        }
    }
}
</script>
<style scoped>
.line {
    animation: stretch 0.3s;
}

.lineShrinking {
    animation: shrink 0.1s;
}

@keyframes stretch {
    0% {
        transform: scale(0.0, 0.0);
    }
    100% {
        transform: scale(1.0, 1.0);
    }
}

@keyframes shrink {
    0% {
        transform: scale(1.0, 1.0);
    }
    100% {
        transform: scale(0.0, 0.0);
    }
}
</style>