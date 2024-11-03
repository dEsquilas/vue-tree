<template>
    <div class="flex flex-row">
        <div class="text-white flex flex-row w-[300px] min-w-[300px] pb-2">
            <div class="current-node flex  flex-grow flex-row align-start" :class="{'with-children': node.children?.length > 0}">
                <figure class="min-w-[11px] min-h-[24px]">
                    <img class="mt-[7px]" src="@/assets/img/arrow.webp" alt="" />
                </figure>
                <div class="node-content block">
                    <div class="px-4 name block min-w-[100px] min-h-[22px]">
                        <span v-show="!enableLabelTextarea"
                              class="bg-gray-800"
                              :class='{"text-gray-400 italic" : node.label?.length === 0 || node.label == "\n"}'
                              v-on:dblclick="focusLabelTextarea">
                            &nbsp;
                            {{ node.label?.length > 0 && node.label !== '\n' ? node.label : 'Empty' }}
                            &nbsp;
                        </span>
                        <textarea
                            ref="labelTextarea"
                            v-model="node.label"
                            v-show="enableLabelTextarea"
                            @keyup.enter="handleEnter"
                            @keydown.tab.prevent="handleTab"
                            @keyup="$emit('updateTextarea')"
                            class="w-full v-textarea bg-gray-800 border border-t-4 border-gray-400 p-1 resize-none ring-0 focus:ring-0 focus:ring-offset-0 focus:outline-none"></textarea>
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
                            @keydown="$emit('updateTextarea')"
                            class="v-textarea bg-gray-800 border border-t-4 border-gray-400 p-1 resize-none ring-0 focus:ring-0 focus:ring-offset-0 focus:outline-none"></textarea>
                    </div>
                </div>
            </div>
            <div class="mt-[6px]" @click="childrenVisibles = !childrenVisibles" v-show="node.children?.length > 0">
                <i v-show="childrenVisibles" class="cursor-pointer flex w-[13px] h-[13px] border border-gray rounded-xl items-center justify-center leading-none pb-[3px]">-</i>
                <i v-show="!childrenVisibles" class="cursor-pointer flex w-[13px] h-[13px] border border-gray rounded-xl items-center justify-center leading-none pb-[2px]">+</i>
            </div>
            <div class="vertical-slash w-[5px] flex flex-col"
                 style="line-height: 1px;"
                 v-show="node.children?.length > 1 && childrenVisibles"
                 :class="{'opacity-0': node.children?.length <= 1}">
                <div class="top-slash w-[5px] h-[5px] mt-[12px]"></div>
                <div class="middle-slash w-[5px]" :style="{'height': childrenHeigth + 'px'}"></div>
                <div class="bottom-slash w-[5px] h-[5px]"></div>
            </div>
        </div>
        <div class="childrenren flex-grow" v-show="childrenVisibles">
            <Node
                @go-next="handleGoNext"
                @update-textarea="calculatechildrenHeights"
                ref="children"
                v-for="chd in node.children"
                :key="chd.id"
                :node="chd" />
        </div>
    </div>
</template>
<script setup>
import { nextTick, ref, onMounted, watch } from 'vue'
import { v4 as uuidv4 } from 'uuid'
import Node from '../components/Node.vue'

const $emit = defineEmits(['goNext', 'updateTextarea'])

const props = defineProps({
    node: {
        type: Object,
        required: true
    }
})

const node = ref(props.node)
const childrenHeigth = ref(0)
const childrenVisibles = ref(true)
const children = ref([])
const enableLabelTextarea = ref(false)
const enableDescriptionTextarea = ref(false)
const labelTextarea = ref(null)
const descriptionTextarea = ref(null)

onMounted(() => {
    if (childrenVisibles.value) {

        nextTick(() => {
            calculatechildrenHeights()
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
        $emit('updateTextarea')
    })

})

watch(childrenVisibles, (newVal) => {
    if (newVal) {
        nextTick(() => {
            calculatechildrenHeights()
        })
    }
})

const calculatechildrenHeights = () => {

    setTimeout(() => {

        childrenHeigth.value = 0

        if (children.value && children.value.length > 1) {
            children.value.forEach((child, index) => {
                if (child.$el && index !== children.value.length - 1) {
                    const childrenHeight = child.$el.offsetHeight;
                    childrenHeigth.value += childrenHeight;
                }
            });
        }

        childrenHeigth.value -= 8

    }, 50)

}

const updateTextarea = () => {
    calculatechildrenHeights()
}

const focusTextarea = (tx) => {
    nextTick(() => {
        tx.value.focus()
        calculatechildrenHeights()
    })

}

const focusLabelTextarea = () => {

    enableLabelTextarea.value = true
    focusTextarea(labelTextarea)
    node.value.label = node.value.label.replace(/\n$/, '')
}

const focusDescriptionTextarea = () => {
    enableDescriptionTextarea.value = true
    focusTextarea(descriptionTextarea)
}

const handleEnter = () => {
    enableLabelTextarea.value = false
    enableDescriptionTextarea.value = false
    $emit('updateTextarea')
    $emit('goNext', node.value.id)
}

const handleTab = () => {

    enableLabelTextarea.value = false
    enableDescriptionTextarea.value = false
    $emit('updateTextarea')

    console.log(node.value.children.length)

    if(node.value.children.length > 0){
        nextTick(() => {
            children.value[0].focusLabelTextarea()
        })
    }
    else{
        node.value.children.push(createChild())
        nextTick(() => {
            children.value[children.value.length - 1].focusLabelTextarea()
        })
    }

}

const handleGoNext = (nodeId) => {

    const nextNodeId = findNextNode(nodeId)

    if(nextNodeId){

        nextTick(() => {
            // Encuentra el índice del nodo siguiente
            const nextNodeIndex = node.value.children.findIndex(child => child.id === nextNodeId);
            if (nextNodeIndex !== -1 && children.value[nextNodeIndex]) {
                children.value[nextNodeIndex].focusLabelTextarea()
            }
        })
    } else {
        const newChild = createChild();
        node.value.children.push(newChild);
        nextTick(() => {
            children.value[children.value.length - 1].focusLabelTextarea()
        })
    }
}

const findNextNode = (nodeId) => {

    // find the next node to nodeId, create new if not exists
    const currentIndex = node.value.children.findIndex((child) => {
        if (child.id === nodeId) {
            return child
        }
    })

    console.log(currentIndex, node.value.children.length)

    return node.value.children.length > currentIndex + 1 ? node.value.children[currentIndex + 1].id : null

}

const createChild = () => {

    return {
        id: uuidv4(),
        label: "",
        description: "",
        children: []
    }
}

defineExpose({
    focusLabelTextarea,
})
</script>
<style>
.current-node.with-children{
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
