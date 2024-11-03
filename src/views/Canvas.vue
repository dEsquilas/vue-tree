<template>
    <section class="min-h-screen min-w-screen bg-gray-800 p-4">
        <Node
            @go-next="handleGoNext"
            ref="children"
            v-for="node in nodes"
            :key="node.id"
            :node="node"
        />
    </section>
</template>
<script setup>
import { nextTick, ref } from 'vue'
import { v4 as uuidv4 } from 'uuid'
import Node from '../components/Node.vue'

const children = ref([])

const nodes = ref([
    {
        id: uuidv4(),
        label: "Node name 1",
        description: "Node description 1",
        children: [
            {
                id: uuidv4(),
                label: "Node name 2",
                children: [
                    {
                        id: uuidv4(),
                        label: "Node name 3",
                        children: []
                    }
                ]
            }
        ]
    },
    {
        id: uuidv4(),
        label: "Node name 4",
        description: "Node description 4",
        children: []
    },
    {
        id: uuidv4(),
        label: "Node name 5",
        description: "Node description 5",
        children: [
            {
                id: uuidv4(),
                label: "Node name 22",
                description: "lalalala lalalala lalalala lalalala lalalala lalalala lalalala lalalala lalalala lalalala lalalala ",
                children: [
                    {
                        id: uuidv4(),
                        label: "Node name 3a",
                        children: []
                    }
                ]
            },
            {
                id: uuidv4(),
                label: "Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 ",
                "description": "Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 ",
                children: [
                    {
                        id: 3,
                        label: "Node name 3f",
                        children: []
                    }
                ]
            },
            {
                id: uuidv4(),
                label: "Node name 23",
                children: [
                    {
                        id: 3,
                        label: "Node name 3f",
                        children: []
                    }
                ]
            },
            {
                id: uuidv4(),
                label: "Node name 23",
                children: [
                    {
                        id: 3,
                        label: "Node name 3f",
                        children: []
                    }
                ]
            }
        ]
    },
    {
        id: uuidv4(),
        label: "Node name 6",
        description: "Node description 6",
        children: []
    }
])

const handleGoNext = (nodeId) => {

    const nextNodeId = findNextNode(nodeId)

    if(nextNodeId){

        nextTick(() => {
            // Encuentra el índice del nodo siguiente
            const nextNodeIndex = nodes.value.findIndex(child => child.id === nextNodeId);
            if (nextNodeIndex !== -1 && children.value[nextNodeIndex]) {
                children.value[nextNodeIndex].focusLabelTextarea()
            }
        })

    } else {
        const newChild = createChild();
        nodes.value.push(newChild);
        nextTick(() => {
            children.value[children.value.length - 1].focusLabelTextarea()
        })
    }

}

const findNextNode = (nodeId) => {

    // find the next node to nodeId, create new if not exists
    const currentIndex = nodes.value.findIndex((child) => {
        if (child.id === nodeId) {
            return child
        }
    })

    return nodes.value.length > currentIndex + 1 ? nodes.value[currentIndex + 1].id : null

}

const createChild = () => {

    return {
        id: uuidv4(),
        label: "",
        description: "",
        children: []
    }
}


</script>
