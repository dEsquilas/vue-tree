<template>
    <div class="flex flex-row">
        <div class="text-white flex flex-row w-[300px] pb-2">
            <div class="current-node flex  flex-grow flex-row align-start" :class="{'with-childs': node.childs?.length > 0}">
                <figure class="min-w-[11px] min-h-[24px]">
                    <img class="mt-[7px]" src="@/assets/img/arrow.webp"  />
                </figure>
                <div class="node-content block">
                    <div class="px-4 name block">
                        <span v-show="!enableLabelTextarea" class="bg-gray-800" v-on:dblclick="focusLabelTextarea">
                            {{ node.label }}
                        </span>
                        <textarea
                            ref="labelTextarea"
                            v-model="node.label"
                            v-show="enableLabelTextarea"
                            @keydown.enter="handleEnter"
                            @keydown="$emit('updateText')"
                            class="v-textarea bg-gray-800 border border-t-4 border-gray-400 p-1 resize-none ring-0 focus:ring-0 focus:ring-offset-0 focus:outline-none"></textarea>
                    </div>
                    <div class="description pl-6 text-xs text-gray-400 max-w-full">
                        <span v-show="!enableDescriptionTextarea" class="bg-gray-800" v-on:dblclick="focusDescriptionTextarea">
                            {{ node.description }}
                        </span>
                        <textarea
                            ref="descriptionTextarea"
                            v-model="node.description"
                            v-show="enableDescriptionTextarea"
                            @keydown.enter="handleEnter"
                            @keydown="$emit('updateText')"
                            class="v-textarea bg-gray-800 border border-t-4 border-gray-400 p-1 resize-none ring-0 focus:ring-0 focus:ring-offset-0 focus:outline-none"></textarea>
                    </div>
                </div>
            </div>
            <div class="mt-[6px]" @click="childsVisibles = !childsVisibles" v-show="node.childs?.length > 0">
                <i v-show="childsVisibles" class="cursor-pointer flex w-[13px] h-[13px] border border-gray rounded-xl items-center justify-center leading-none pb-[3px]">-</i>
                <i v-show="!childsVisibles" class="cursor-pointer flex w-[13px] h-[13px] border border-gray rounded-xl items-center justify-center leading-none pb-[2px]">+</i>
            </div>
            <div class="vertical-slash w-[5px] flex flex-col"
                 style="line-height: 1px;"
                 v-show="node.childs?.length > 1 && childsVisibles"
                 :class="{'opacity-0': node.childs?.length <= 1}">
                <div class="top-slash w-[5px] h-[5px] mt-[12px]"></div>
                <div class="middle-slash w-[5px]" :style="{'height': childsHeigth + 'px'}"></div>
                <div class="bottom-slash w-[5px] h-[5px]"></div>
            </div>
        </div>
        <div class="children flex-grow" v-show="childsVisibles">
            <Node @update-text="calculateChildHeights" ref="childs" v-for="child in node.childs" :key="child.id" :node="child" />
        </div>
    </div>
</template>
<script setup>
import { nextTick, ref, onMounted, watch } from 'vue'
import Node from '../components/Node.vue'

const $emit = defineEmits(['updateText'])

const props = defineProps({
    node: {
        type: Object,
        required: true
    }
})

const node = ref(props.node)
const childsHeigth = ref(0)
const childsVisibles = ref(true)
const childs = ref([])
const enableLabelTextarea = ref(false)
const enableDescriptionTextarea = ref(false)
const labelTextarea = ref(null)
const descriptionTextarea = ref(null)


onMounted(() => {
    if (childsVisibles.value) {

        nextTick(() => {
            calculateChildHeights()
        })

    }

    // detect click outside and close textarea
    window.addEventListener('click', (e) => {
        if (e.target !== enableDescriptionTextarea.value) {
            enableDescriptionTextarea.value = false
        }
        if (e.target !== enableLabelTextarea.value) {
            enableLabelTextarea.value = false
        }
    })

})

watch(childsVisibles, (newVal) => {
    if (newVal) {
        nextTick(calculateChildHeights())
    }
})

const calculateChildHeights = () => {

    setTimeout(() => {

        childsHeigth.value = 0

        if (childs.value && childs.value.length > 1) {
            childs.value.forEach((child, index) => {
                if (child.$el && index !== childs.value.length - 1) {
                    const childHeight = child.$el.offsetHeight;
                    childsHeigth.value += childHeight;
                }
            });
        }

        childsHeigth.value -= 8

    }, 100)

}

const updateText = () => {
    console.log("u")
    calculateChildHeights()
}

const focusTextarea = (tx) => {
    nextTick(() => {
        tx.value.focus()
    })
}

const focusLabelTextarea = () => {
    enableLabelTextarea.value = true
    focusTextarea(labelTextarea)
}

const focusDescriptionTextarea = () => {
    enableDescriptionTextarea.value = true
    focusTextarea(descriptionTextarea)
}

const handleEnter = () => {
    enableLabelTextarea.value = false
    enableDescriptionTextarea.value = false
}


</script>
<style>
.current-node.with-childs{
    background: url('@/assets/img/h-slash.webp') repeat-x center 12px;
}

.top-slash{
    background: url('@/assets/img/top-slash.webp') repeat-x center;
}

.middle-slash{
    background: url('@/assets/img/v-slash.webp') repeat-y center;
}

.bottom-slash{
    background: url('@/assets/img/bottom-slash.webp') repeat-x center;
}

.v-textarea{

    field-sizing: content;

}
</style>
