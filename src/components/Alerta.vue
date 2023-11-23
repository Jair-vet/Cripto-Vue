<script setup>
    import { computed } from 'vue';

    const props = defineProps({
        alerta: {
            type: Object,
            required: true
        }
    })

    const isError = computed(() => {
        return props.alerta.tipo === 'error'
    })
</script>

<template>
    <div class="alerta">
        <slot></slot>
    </div>
</template>

<style>
    .alerta {
        text-align: center;
        margin-bottom: 2rem;
        color: var(--blanco);
        background-color: rgb(196, 23, 23);
        text-transform: uppercase;
        font-weight: 700;
        font-size: 1.4rem;
        padding: 1rem;
        border-radius: 5px;
    }
</style> 