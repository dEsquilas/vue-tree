<template>
    <div class="text-white flex flex-row pb-2">
        <div class="current-node min-w-1/6 w-1/6 flex flex-row align-start" :class="{'with-childs': node.childs?.length > 0}">
            <figure>
                <img class="mt-[7px]" src="@/assets/img/arrow.webp"  />
            </figure>
            <div class="node-content bg-gray-800 ">
                <div class="name pl-4">
                    <span class="pr-4">
                        {{ node.label }}
                    </span>
                </div>
                <div class="description pl-6 text-xs text-gray-400">
                    {{ node.description }}
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
        <div ref="childsBlock" class="children flex-grow" v-show="childsVisibles">
            <Node v-for="child in node.childs" :key="child.id" :node="child" />
        </div>
    </div>
</template>
<script setup>
import { ref, onMounted } from 'vue'
import Node from '../components/Node.vue'

const props = defineProps({
    node: {
        type: Object,
        required: true
    }
})

const node = ref(props.node)
const childsHeigth = ref(34)
const childsVisibles = ref(true)
const childsBlock = ref(null)


onMounted(() => {
    setContentHeight()
})



const setContentHeight = () => {
    //childsHeigth.value = childsBlock.value.offsetHeight
    if(node.value.childs.length > 1)
        console.log(childsBlock.value.offsetHeight)
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
</style>
