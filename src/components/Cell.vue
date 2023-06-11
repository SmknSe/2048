<template>
    <div class="cell" :style="this.classic_style ? {backgroundColor: computed_bg}: {border: `solid ${computed_bg} 5px`, color:computed_bg}">
        {{ this.formatted_val }}
    </div>
</template>

<script>

export default {
    name: 'Cell',
    props: {
        value: Number,
        classic_style: false
    },
    data() {
        return {
            color_scheme : {
                2: '(238, 228, 218)',
                4: '(250, 184, 132)',
                8: '(255, 150, 112)',
                16: '(250, 128, 115)',
                32: '(252, 117, 83)',
                64: '(247, 109, 109)',
                128: '(255, 246, 115)'
            }
        }
    },
    computed: {
        formatted_val() {
            return this.value == 0 ? '' : this.value
        },
        computed_bg() {
            if (this.formatted_val == '') return 'rgba(238, 228, 218, 0.35)'
            else if (this.formatted_val >= 128) return `rgba${this.color_scheme[128]}`
            else return `rgba${this.color_scheme[this.formatted_val]}`
        }
    }
}
</script>

<style>
.cell{
    transition: border,color,background-color .3s ease-in-out;
}
</style>