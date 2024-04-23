<script setup>
import { ref } from 'vue'

const props = defineProps({
    imgBackFaceUrl: {
        type: String,
        required: true,
    },
    card: {
        type: [Number, String, Array, Object],
        required: true,
    },
    cardLength: {
        type: Number,
        required: true,
    }
});
const isFlipped = ref(false);
const isDisabled = ref(false);

const emits = defineEmits({
    onFlip: (card) => {},
});

function onToggleFlipCard() {
    if (isDisabled.value) return true;

    isFlipped.value = !isFlipped.value;

    if (isFlipped) {
        emits('onFlip', props.card);
    }
}

function onFlipBackCard() {
    isFlipped.value = false;
}

function onDisabledMode() {
    isDisabled.value = true;
}

defineExpose({
    onFlipBackCard,
    onDisabledMode,
});
</script>

<template>
    <div class="card" :class="{ disabled: isDisabled }" :style="{
        height: `${(920 - 16 * 4) / Math.sqrt(props.cardLength) - 16 }px`,
        width: `${
            (((920 - 16 * 4) / Math.sqrt(props.cardLength) - 16) * 3) / 4
        }px`,
        perspective: `${
            ((((920 - 16 * 4) / Math.sqrt(props.cardLength) - 16) * 3) / 4) * 2
        }px`,
    }">
        <div class="card__inner" :class="{ 'is-flipped': isFlipped }" @click="onToggleFlipCard">
            <div class="card__face card__face--front">
                <div
                    class="card__content"
                    :style="{
                    'background-size': `${
                        (((920 - 16 * 4) / Math.sqrt(props.cardLength) - 16) * 3) / 4 / 3 }px
                    ${
                        (((920 - 16 * 4) / Math.sqrt(props.cardLength) - 16) * 3) / 4 / 3 }px`,
                    }"
                ></div>
            </div>
            <div class="card__face card__face--back">
                <div class="card__content" :style="{
                    backgroundImage: `url('${require('@/assets/' + props.imgBackFaceUrl)}')`,
                }"></div>
            </div>
        </div>
    </div>
</template>

<style lang='css' scoped>
    .card {
        display: inline-block;
        margin-right: 1rem;
        margin-bottom: 1rem;
        width: 90px;
        height: 120px;
        perspective: 100;
    }

    .card__inner {
        width: 100%;
        height: 100%;
        transition: transform 1s;
        transform-style: preserve-3d;
        cursor: pointer;
        position: relative;
    }

    .card.disabled .card__inner {
        cursor: default;
    }

    .card__inner.is-flipped {
        transform: rotateY(-180deg);
    }

    .card__face {
        position: absolute;
        width: 100%;
        height: 100%;
        backface-visibility: hidden;
        overflow: hidden;
        border-radius: 1rem;
        padding: 1rem;
        box-shadow: 0 3px 18px 3px rgba(0, 0, 0, 0.2);
    }

    .card__face--front .card__content {
        background: url("../assets/images/icon_back.png") no-repeat center center;
        background-size: 40px 40px;
        height: 100%;
        width: 100%;
    }

    .card__face--back {
        background-color: var(--light);
        transform: rotateY(-180deg);
    }

    .card__face--back .card__content {
        background-position: center center;
        background-repeat: no-repeat;
        background-size: contain;
        height: 100%;
        width: 100%;
    }
</style>