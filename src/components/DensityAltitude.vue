<script setup lang="ts">
    import {ref, watch} from "vue"
    import {getDensityAltitude} from "../lib/physics"
    import {unit} from "mathjs"

    const densityAltitudeText = ref("");
    const props = defineProps({
        elevation: {type: Number, required: true},
        temperature: {type: Number, required: true},
        dewpoint: {type: Number, required: true},
        altimeter:  {type: Number, required: true},
    });
    const emit = defineEmits<{
        (e: 'emitDensityAltitude', densityAltitude: number): void
    }>()
    watch(() =>
    [
        props.elevation,
        props.temperature,
        props.dewpoint,
        props.altimeter,
    ] as const,
    ([
        elevationVal,
        temperatureVal,
        dewpointVal,
        altimeterVal,
    ]) =>
    {
        var densityAltitude = getDensityAltitude(
            unit(temperatureVal, "degC"),
            unit(dewpointVal, "degC"),
            unit(altimeterVal, "inHg"),
            unit(elevationVal, "feet")
        )
        let val = densityAltitude.toNumeric("feet") as number
        densityAltitudeText.value = `Density altitude (estimated): ${val.toFixed(0)}`;
        emit('emitDensityAltitude', Math.round(val));
    });
</script>

<template>
    <p id="densityAltitudeText">{{densityAltitudeText}}</p>
</template>

<style scoped>
    p {
        font-size: 12px;
    }
</style>