<script setup>
import { ref } from "vue";
import CardFlip from "./Card.vue";

const rules = ref([]);
const cards = ref({});

const props = defineProps({
    cardsContext: {
        type: Array,
        default() {
            return [];
        }
    }
});

const emits = defineEmits({
    onFinish: () => {},
});

function checkRule(card) {
    if (rules.value.length === 2) return false;

    rules.value.push(card);
    
    if (rules.value.length === 2 && rules.value[0].value === rules.value[1].value) {
        cards.value[`card-${rules.value[0].index}`].onDisabledMode();
        cards.value[`card-${rules.value[1].index}`].onDisabledMode();
        rules.value = [];

        const disabledElement = document.querySelectorAll(
            ".screen .card.disabled"
        );

        if (disabledElement && disabledElement.length === props.cardsContext.length - 2) {
            setTimeout(() => {
                emits('onFinish');
            }, 920);
        }
    } else if (rules.value.length === 2 && rules.value[0].value !== rules.value[1].value) {
        setTimeout(() => {
            cards.value[`card-${rules.value[0].index}`].onFlipBackCard();
            cards.value[`card-${rules.value[1].index}`].onFlipBackCard();
            rules.value = [];
        }, 800);

    } else return false;
}

</script>

<template>
    <div class="screen">
        <div class="screen__inner" :style="{
            width: `${
                ((((920 - 16 * 4) / Math.sqrt(props.cardsContext.length) - 16) * 3) / 4 +
                    16) * Math.sqrt(props.cardsContext.length)
                }px`,
            }"
        >
            <card-flip
                v-for="(card, index) in props.cardsContext"
                :key="index"
                :ref="(el) => cards[`card-${index}`] = el"
                :imgBackFaceUrl="`images/${card}.png`"
                :card="{ index: index, value: card }"
                :cardLength="props.cardsContext.length"
                @onFlip="checkRule($event)"
            />
        </div>
    </div>
</template>

<style scoped>
.screen {
    width: 100%;
    height: 100vh;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 2;
    background-color: var(--dark);
    color: var(--light);
}

.screen__inner {
    width: calc(424px);
    display: flex;
    flex-wrap: wrap;
    margin: 2rem auto;
}
</style>