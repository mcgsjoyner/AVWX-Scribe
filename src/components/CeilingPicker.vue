<script setup lang="ts">
    import {ref, watch} from "vue"
    import CustomRange from './CustomRange.vue'

    const title = "Ceiling"
    const realValue = ref(0);
    const start = 1.0;
    const high = 20000;
    const low = 0;
    const optimum = 5000;
    const gradient = 0.4;
    const sketchy = 3000;
    const bad = 1000;
    const disabled = ref(true);
    const disablingCoverages = ["SKC", "NCD", "CLR", "VV"];

    const props = defineProps({
        cloudCoverage: {
            type: String,
            default: "SKC",
            required: true,
        }
    });

    function get_read_out() {
        let valid = `${Math.round(realValue.value / 100).toFixed(0).padStart(3, '0')} (x 100 ft)`;
        return disabled.value ? "NONE" : valid;
    };

    watch(() => props.cloudCoverage, (newVal) => {
        disabled.value = disablingCoverages.includes(newVal);
    })

    const emit = defineEmits<{
        (e: 'emitCeiling', ceiling: number): void
    }>()
    const onInput = () => {
        realValue.value = Math.round(realValue.value / 100) * 100;
        emit('emitCeiling', realValue.value);
    }
    onInput();
</script>

<template>
    <CustomRange
        :title = "title"
        :start = "start"
        :high = "high"
        :low = "low"
        :optimum = "optimum"
        :gradient = "gradient"
        :sketchy = "sketchy"
        :bad = "bad"
        @input = "onInput"
        @emit-value="(payload: number) => {realValue = payload}"
        :readOut = "get_read_out()"
        :disabled = "disabled"
        :numDigits = -2
    />
</template>
